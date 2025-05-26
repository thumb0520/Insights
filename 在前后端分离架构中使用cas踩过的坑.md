# 引言

最近在工作中接到了一个活——对接一个系统的cas登录体系，为我们自己的应用提供身份认证。对方信誓旦旦说道：纯后端就能做，很简单的。年少单纯的我在内心窃喜，心想：真好，又是我略微出手便能休息几天的节奏。直到上手了我才发现原来其中另有乾坤，特此记录，以示后人警醒。

# CAS是什么

CAS是Central Authentication Service的缩写，中央认证服务，一种独立开放指令协议。CAS 是耶鲁大学（Yale University）发起的一个开源项目，旨在为 Web 应用系统提供一种可靠的单点登录方法。整体的认证流程简单来说分为四个步骤。

1. 访问自己的服务，自己的服务发现没有身份信息，诱导跳转至认证服务器。
2. 认证服务器登录后，携带ticket凭证，重定向回自己的服务。
3. 自己的服务携带ticket凭证，请求认证服务器获取该ticket对应的身份信息。
4. 通过身份信息校验，继续正常访问服务。

这一套机制可能在前后端未分离的架构服务中很是高效，但是在目前前后端分离的大背景下，确是显的有些不解风情了。

# CAS引用

在pom依赖中添加如下依赖、在配置文件写下如下配置后，就开始愉快的踩坑之旅吧。

```xml
<!-- https://mvnrepository.com/artifact/net.unicon.cas/cas-client-autoconfig-support -->
<dependency>
    <groupId>net.unicon.cas</groupId>
    <artifactId>cas-client-autoconfig-support</artifactId>
    <version>2.3.0-GA</version>
</dependency>
```

```yaml
#cas配置
cas:
  server-url-prefix: http://cas-server/cas
  server-login-url: http://cas-server/cas/login?appid=xxxxxxx
  client-host-url: http://client-server
  use-session: true
  validation-type: cas
```



# CAS第一难：重定向之难上天

万万没有想到就在第一步遇到了难题。在前后端分离的项目中，前端向后端请求接口，后端发现没有身份信息，想要跳转到认证服务器。此时一个简简单单的http响应码302加上一个孱弱的location重定向地址远远不够打动现在无情的浏览器安全机制。无情的CORS死死地拦住了去路。无奈只能从源码中获取一点灵感。把目光投向今天的第一个filter：org.jasig.cas.client.authentication.AuthenticationFilter。

关注org.jasig.cas.client.authentication.AuthenticationFilter#doFilter方法，不难发现一些端倪。

```java
    public final void doFilter(final ServletRequest servletRequest, final ServletResponse servletResponse,
            final FilterChain filterChain) throws IOException, ServletException {
        
        final HttpServletRequest request = (HttpServletRequest) servletRequest;
        final HttpServletResponse response = (HttpServletResponse) servletResponse;
        
        if (isRequestUrlExcluded(request)) {
            logger.debug("Request is ignored.");
            filterChain.doFilter(request, response);
            return;
        }
        
        final HttpSession session = request.getSession(false);
        final Assertion assertion = session != null ? (Assertion) session.getAttribute(CONST_CAS_ASSERTION) : null;

        if (assertion != null) {
            filterChain.doFilter(request, response);
            return;
        }

        final String serviceUrl = constructServiceUrl(request, response);
        final String ticket = retrieveTicketFromRequest(request);
        final boolean wasGatewayed = this.gateway && this.gatewayStorage.hasGatewayedAlready(request, serviceUrl);

        if (CommonUtils.isNotBlank(ticket) || wasGatewayed) {
            filterChain.doFilter(request, response);
            return;
        }

        final String modifiedServiceUrl;

        logger.debug("no ticket and no assertion found");
        if (this.gateway) {
            logger.debug("setting gateway attribute in session");
            modifiedServiceUrl = this.gatewayStorage.storeGatewayInformation(request, serviceUrl);
        } else {
            modifiedServiceUrl = serviceUrl;
        }

        logger.debug("Constructed service url: {}", modifiedServiceUrl);

        final String urlToRedirectTo = CommonUtils.constructRedirectUrl(this.casServerLoginUrl,
                getProtocol().getServiceParameterName(), modifiedServiceUrl, this.renew, this.gateway);

        logger.debug("redirecting to \"{}\"", urlToRedirectTo);
        this.authenticationRedirectStrategy.redirect(request, response, urlToRedirectTo);
    }
```

最后一行表明了他是如何完成的重定向。再继续追踪到authenticationRedirectStrategy的默认实现类：org.jasig.cas.client.authentication.DefaultAuthenticationRedirectStrategy。这个默认实现类相当简单，只有一行。

```java
public final class DefaultAuthenticationRedirectStrategy implements AuthenticationRedirectStrategy {

    public void redirect(final HttpServletRequest request, final HttpServletResponse response,
            final String potentialRedirectUrl) throws IOException {
        response.sendRedirect(potentialRedirectUrl);
    }
}
```

返回到org.jasig.cas.client.authentication.AuthenticationFilter中，发现urlToRedirectTo其实就是配置中的$casServerLoginUrl?service=request.uri。需要注意的是生成的重定向地址是后端的接口路径——在前后端分离的项目中，你当然不知道前端的路径。在此处埋下第一个伏笔。

至此针对第一步重定向跨域的问题我们也已经有了眉目。重写掉org.jasig.cas.client.authentication.DefaultAuthenticationRedirectStrategy,我们自己的重定向策略要交还给前端，让前端来完成重定向到认证服务器的动作。于是我们大手一挥，写下了三行代码。

```java
public class MyCasAuthenticationRedirectStrategy implements AuthenticationRedirectStrategy {
    public MyCasAuthenticationRedirectStrategy() {
    }

    @Override
    public void redirect(HttpServletRequest request, HttpServletResponse response, String potentialRedirectUrl) throws IOException {
        response.setStatus(200);
        response.getWriter().write(String.format("{\"code\":3020,\"msg\":\"redirect\",\"content\":\"%s\"}", potentialRedirectUrl));
    }
}
```

待前端也完成了相应的改造后我们就能发现浏览器页面停留在认证服务器的登录页，我们成功走完了第一步。

# CAS第二难：跳回服务不见前端

在第一步正确输入账号密码后，认证服务器会跳回我们的服务，但是奇怪的是我们只能看到了一个接口的响应。我们可以在第一步的potentialRedirectUrl中发现一些端倪。返回的接口路径恰恰就是potentialRedirectUrl里携带的service参数路径。原来我们第一步的改造仅仅成功了一半，认证服务器重定向回我们的服务怎么能重定向到后端接口路径呢？改！MyCasAuthenticationRedirectStrategy又变了一个样子。传入redirectUrl直接就把我们的前端地址写上：

```java
public class MyCasAuthenticationRedirectStrategy implements AuthenticationRedirectStrategy {

    private String redirectUrl;

    public MyCasAuthenticationRedirectStrategy() {
    }

    public MyCasAuthenticationRedirectStrategy(String redirectUrl) {
        this.redirectUrl = redirectUrl;
    }

    @Override
    public void redirect(HttpServletRequest request, HttpServletResponse response, String potentialRedirectUrl) throws IOException {
        response.setStatus(200);
        response.getWriter().write(String.format("{\"code\":3020,\"msg\":\"redirect\",\"content\":\"%s\"}", getRedirectUrl()));
    }

    public String getRedirectUrl() {
        return redirectUrl;
    }

    public void setRedirectUrl(String redirectUrl) {
        this.redirectUrl = redirectUrl;
    }
}
```

在传入的redirectUrl变为前端地址后，会发现整个页面在不停地鬼畜刷新，这是因为从认证服务器带回的ticket的凭证仅仅停留在了我们应用的前端，此时当前端向后端发起请求时，后端无法获取到认证服务器带回的ticket参数，所以后端又一次返回了重定向地址。我们只需要让前端将浏览器地址上的ticket参数带着请求后端接口，就可以打断这个无限的循环了。

# CAS第三难：认证ticket货不对板

当我们满心欢喜让前端把ticket携带着请求后端，盼望一切顺利时，现实又给予了我们重重的一击。不过好在这已经是最有一击，解决完了这个问题后，我们就可以给CAS一记重拳KO在地了。赶紧来看看这是什么问题吧。

当前端发起了携带着ticket的请求，我们的后台会抛出500异常，打印了一大堆的报错。大致如下：

```bash
cause of ServletException.
org.jasig.cas.client.javafilter.validation.TicketValidationException: 未能够识别出目标 'ST-887-ikaqqy3baEfkerR24ZUP-cas01.example.org'票根.......
```

询问了认证服务器的同事后才得知，在请求ticket对应的登录人信息时，要求此时的service地址和请求ticket时使用的service地址一致。当二者不一致时，就会发生如上的报错。我们在第二步改造重定向地址时，为了使认证服务器能重定向回我们的前端应用，使用的service地址是前端应用地址。但是在请求ticket对应的登录人信息时，依旧只有后端接口路径，没有前端应用地址。两次请求的service地址不一致了。知道了原因问题也就好改了。跟踪源码:org.jasig.cas.client.validation.Cas20ProxyReceivingTicketValidationFilter --> org.jasig.cas.client.validation.AbstractTicketValidationFilter 

在org.jasig.cas.client.validation.AbstractTicketValidationFilter里可以看到这个filter的doFilter方法。

```java
public final void doFilter(final ServletRequest servletRequest, final ServletResponse servletResponse,
            final FilterChain filterChain) throws IOException, ServletException {

        if (!preFilter(servletRequest, servletResponse, filterChain)) {
            return;
        }

        final HttpServletRequest request = (HttpServletRequest) servletRequest;
        final HttpServletResponse response = (HttpServletResponse) servletResponse;
        final String ticket = retrieveTicketFromRequest(request);

        if (CommonUtils.isNotBlank(ticket)) {
            logger.debug("Attempting to validate ticket: {}", ticket);

            try {
                final Assertion assertion = this.ticketValidator.validate(ticket,
                        constructServiceUrl(request, response));

                logger.debug("Successfully authenticated user: {}", assertion.getPrincipal().getName());

                request.setAttribute(CONST_CAS_ASSERTION, assertion);

                if (this.useSession) {
                    request.getSession().setAttribute(CONST_CAS_ASSERTION, assertion);
                }
                onSuccessfulValidation(request, response, assertion);

                if (this.redirectAfterValidation) {
                    logger.debug("Redirecting after successful ticket validation.");
                    response.sendRedirect(constructServiceUrl(request, response));
                    return;
                }
            } catch (final TicketValidationException e) {
                logger.debug(e.getMessage(), e);

                onFailedValidation(request, response);

                if (this.exceptionOnValidationFailure) {
                    throw new ServletException(e);
                }

                response.sendError(HttpServletResponse.SC_FORBIDDEN, e.getMessage());

                return;
            }
        }

        filterChain.doFilter(request, response);

    }
```

再继续跟踪this.ticketValidator.validate(ticket,constructServiceUrl(request, response))。

```java
    public final Assertion validate(final String ticket, final String service) throws TicketValidationException {
        final String validationUrl = constructValidationUrl(ticket, service);
        logger.debug("Constructing validation url: {}", validationUrl);

        try {
            logger.debug("Retrieving response from server.");
            final String serverResponse = retrieveResponseFromServer(new URL(validationUrl), ticket);

            if (serverResponse == null) {
                throw new TicketValidationException("The CAS server returned no response.");
            }

            logger.debug("Server response: {}", serverResponse);

            return parseResponseFromServer(serverResponse);
        } catch (final MalformedURLException e) {
            throw new TicketValidationException(e);
        }
    }
```

debug到这里的validationUrl就可以清晰地看到请求ticket对应的登录人信息的url地址，service参数填写的就是后端的接口路径地址。我们只需要在这里改动一下，让url拼接上的service参数是我们在第二步改造过程中使用前端应用地址就可以了。至此我们终于可以成功的完成CAS登录认证了，第一个成功的接口会自动写入cookie，后续的接口调用时，只需要携带cookie就可以完成调用了。

# 总结

现在回顾总结一下我们的整体改造方案。

1. 重写掉org.jasig.cas.client.authentication.DefaultAuthenticationRedirectStrategy，改造原先的http响应码302重定向地址，改为http响应码为200，交由项目前端完成重定向至认证服务器操作。携带的service参数使用项目前端地址，保证从认证服务器处重定向回到前端。
2. 重写掉org.jasig.cas.client.validation.AbstractUrlBasedTicketValidator，改造validationUrl，使用前端地址作为service参数请求认证服务器获取ticket对应的登录人信息。
3. 前端改造：在业务接口之前前置一个权限的接口，没有身份信息时，重定向到认证服务器。从认证服务器回来后，再从浏览器地址栏上获取ticket，携带着ticket请求权限接口，返回携带cookie信息，再继续正常请求业务接口。

# 尾声

CAS认证总共就用到了两个filter，没想到在前后端分离的架构中，这两个filter都要进行改造，属实是有点坑了。特在此记录。之后如果哪天有时间再整理一下基于oauth2协议实现的统一认证过程中遇到的坑。