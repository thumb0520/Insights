# Agent分享资料

## 一、LLM Agent概述

### （一）定义

大模型Agent是一种构建于大型语言模型（LLM）之上的智能体，它具备环境感知能力、自主理解、决策制定及执行行动的能力。能够模拟独立思考过程，灵活调用各类工具，逐步达成预设目标。在技术架构上，Agent从面向过程的架构转变为面向目标的架构，旨在通过感知、思考与行动的紧密结合，完成复杂任务。

例如，OpenAI应用人工智能研究负责人Lilian Weng在其Blog中将LLM Agent定义为LLM、记忆（Memory）、任务规划（Planning Skills）以及工具使用（Tool Use）相集合的智能系统。其中LLM是核心大脑，Memory、Planning Skills以及Tool Use等则是Agents系统实现的三个关键组件。通过LLM和三个关键组件，LLM Agent可以拥有复杂的工作流程，其中模型基本上是与自身对话，而无需人工参与到交互的每个部分。

### （二）核心组件

#### 1、核心LLM

LLM Agent的基础是大型语言模型，它经过大量数据集训练，能生成和理解简单文本。LLM的规模和设计决定了Agent的初始能力和限制。例如，GPT - 4、Claude 3.5等闭源模型以及Llama 3.2、Qwen 2.5等开源模型都可作为LLM Agent的核心。

#### 2、Prompt（提示）

Prompt用于激活和引导LLM的能力。通过精心设计的提示，向Agent传达其身份、专业知识、行为和目标。例如，在对话Agent中，提示可编码角色以采用不同的语音；在任务导向的Agent中，提示可明确目标、提供相关信息并构建指令。

#### 3、Memory（记忆）

1. **短期记忆**：所有上下文学习依赖模型的短期记忆能力，用于处理当前任务，如对话记录。
2. **长期记忆**：使AI Agents能够长期保存和调用无限信息，通常通过外部载体存储和快速检索实现，如向量数据库，可存储用户特征、业务数据等。

#### 4、工具使用（Tool use）

LLM Agent可以学习调用外部API，以获取模型权重中缺少的额外信息，如当前信息、代码执行能力、对专有信息源的访问等。例如，在回答关于最新股票价格的问题时，Agent可以使用网络搜索工具获取实时信息。常见工具包括代码执行、网络搜索、文件读取和数据分析等。

## 二、Agent发展

### **（一）从单Agent到多Agent**

  随着大模型驱动的智能体（LLM Agent）在各类场景中落地，大家发现了一个有趣的矛盾：单个智能体越  “聪明”，实际应用中反而越容易遇到瓶颈。就像一位全能的超人，既要解读用户指令，又要规划任务、调用工具，还要实时纠错——多线程的 “大脑过载”  让响应速度变慢，复杂任务拆解也容易顾此失彼。尤其是在面对复杂的开放性问题时，**单Agent知识储备有限、处理能力不足、难以适应动态变化的环境**，这使得它**很难有效拆分任务或协调多个目标**。因此，人们很快意识到，现实世界的复杂问题往往需要团队作战。例如，医疗诊断需要分诊、检验、影像分析等环节接力；电商促销涉及选品、定价、客服等多部门协同。在这些场景中，单个能力再强的智能体也只能像一位孤掌难鸣的专家那样，难以避免信息断层与流程阻塞。

  相比之下，多 Agent 系统就像一支配合默契的团队：每位成员专注处理自己擅长的子任务，通过**分工协作**让复杂问题迎刃而解。这种模式不仅大幅提升效率，还能让整个系统更稳定可靠。就像在一个软件开发团队中，产品经理梳理需求、架构师搭建框架、工程师专注编码，每个角色都能产出标准化成果（如需求文档、接口规范）并在协作中保持思路统一，减少因环节衔接不畅导致的错误。团队通过共享信息库和智能通知机制，避免重复沟通的困扰。这样的协作体系能灵活调整团队配置，随时增减专业人员来满足不同任务需求。更有趣的是，多 Agent 甚至能主动完善需求细节，例如自动优化界面设计并形成更完善的方案，不再需要依赖人工预设规则。

  当然，多 Agent 协作模式也带来了新的挑战。

  **首先是信息整合的难题**。共享信息池如同一个公共留言板，海量信息涌入时需要依赖**智能筛选机制**，而角色之间的对话必须遵循**结构化协议**（例如统一使用标准化模板），否则容易产生 "你说东我理解西" 的错位问题。如何给不同 AI 角色分配任务、设计合理的对话规则（比如防止信息误传的校验机制），这些都需要仔细考虑。

  **其次是资源协调的考验**。当团队成员（Agent）数量增加时，就像办公室突然涌入大量新人，设备抢用、空间不足等问题会突显。尤其是在需要即时反馈的场景中，响应速度变慢会让用户明显感到卡顿。更棘手的是，当多个 AI 协同工作时，**决策责任的划分、隐私数据的保护等问题**就像团队作业时的权责归属，需要建立更清晰的安全管理机制。尽管存在这些局限性，多 Agent 还是为 LLM 驱动的自主协作提供了范式，为复杂任务的自动化开发奠定了基础。

  在多 Agent  的协作模式诞生后，大家发现，虽然这种分布式架构带来了灵活的可能性，但如果缺少统一的框架支持，实际落地时会遇到不少挑战。例如，每个 Agent  的功能都需要从零开始设计和对接，光是协调不同模块之间的交互逻辑就可能消耗大量时间，导致开发周期很长。系统运行时也可能因为某个 Agent  的响应延迟或逻辑冲突，引发连锁反应。与此同时，当业务需要扩展新能力时，开发者往往需要反复校准各个 Agent 的协作关系。这种  "牵一发而动全身" 的改动成本，让系统迭代变得束手束脚。更不用说日常维护时，排查问题就像在错综复杂的迷宫里寻找线索，不同 Agent  的日志格式、通信协议如果缺乏统一标准，每次排查都得重新理解每个模块的 "方言"。这时候大家才意识到，一套好的框架不仅能规范开发流程，而且可以为多 Agent 协作搭建起高速公路，让团队把精力真正聚焦在创造价值上。

  由此，**多 Agent  框架应运而生。**这类框架的核心思想在于，将**协作逻辑抽象出来并建立标准化的通信规则和任务分配方式**，从而可以使不同功能的智能体形成有机整体。这样，Agent开发者**无需再从零开始设计协作规则**，而是**专注于提升单个智能体的专业能力**。这种设计既降低了系统搭建的复杂性，又能让智能体团队更高效地应对复杂问题。

### （二）常见的多Agent结构

##### 1、**顺序工作流** 

考虑这样一个客户服务场景：客户正和一个聊天机器人交互，希望咨询某公司产品的详细情况。显然，这个任务可以很容易被拆分为三个有前后关系的子任务，分别是检索、撰写和校对，因此可以设计如下三个智能体。

- **检索智能体：**分析用户描述，从产品库中找到合适的产品，输出产品描述信息。它的提示词是：“你是某公司的营销经理。请根据用户给出的产品描述，从产品库中找到合适的产品。你可以用模糊搜索API找到相似的产品，然后挑选一个最符合用户需求的产品。请输出你挑选的商品的描述。”
- **撰写智能体：**根据检索智能体提供的产品描述信息撰写文案，输出一份文案草稿。它的提示词是：“你是某公司的营销文案。给定一段商品的描述，撰写一份引人注目的营销文案，激发消费者的购买热情。输出应简短（约 150 个字），且只输出一个文本块。”
- **校对智能体：**完善语法、提高清晰度和保持一致的语气来润色文案草稿，输出最终提供给用户的最终版本。它的提示词是：“你是某公司的营销文案编辑。给定一则营销文案草稿，纠正语法，提高清晰度，确保语气一致，给出格式并使其精炼。将改进后的最终稿件输出为一个文本块。”

![img](https://i-blog.csdnimg.cn/img_convert/6e073223f52e7267058a84a18e77cd38.png)

在这个流程中，智能体按照确定的顺序做出响应，工作流中的每个智能体处理消息并生成响应，然后将其传递给下一个智能体，从而协作执行任务。这种设计被称为“**顺序工作流**”，是一种十分基础的多智能体设计模式，适合创建确定性工作流，每个智能体只需完成预先指定的子任务。

##### 2、**组聊天** 

考虑这样一个服务场景：某公司员工正和一个聊天机器人交互，希望完成一份图文并茂的商品宣传文案的撰写工作。显然，这个任务也可以被拆为三个子任务：文案撰写、图片绘制和意见反馈，但是它们却没有严格的顺序关系。例如，当文案撰写完成后，主管发现文案写的没有很好的满足用户需求，所以需要要求撰写者返工，返工合格后会交由美工智能体绘制插图，最后主管审查无误则交付给用户。在这个过程中，是否需要撰写者或美工智能体返工是不确定的，选用“**顺序工作流**”实现无疑不是一个好的选择。

在现实场景中，如果真的有这样一份宣传文案需要撰写，正常做法通常是和文案、美工拉一个微信群，然后把需求扔到群里，反复审查直到任务结束。所以不妨也采取这样的方法协调智能体的工作，可以设计如下这样四个智能体并把它们拉到一个群聊天中：

- **组聊天管理智能体：**负责决定当前由哪个智能体发言。它的提示词是：“你正在组织一个讨论。您可以选择以下角色进行发言：{**角色**}。阅读下面的聊天消息。然后从{**参与者**}中选择下一个角色继续回答问题。请只返回角色名。{**聊天消息**}”
- **文案撰写智能体：**根据要求生成生成一份针对某商品的介绍文案。它的提示词是：“你是某公司的一名商品文案撰写人员，你擅长根据要求撰写图文并茂的商品介绍。当你需要在文案中添加插图，只需在相应位置描述插图即可。”
- **美工智能体：**根据要求生成一张商品文案中的插图或者海报。它的提示词是：“你是某公司的一名美工，你擅长使用生成图像工具（DALL-E）根据要求绘制商品的介绍插图或海报。必须确保这些图片具有一致的风格。”
- **主管智能体：**为美工智能体和文案撰写智能体安排工作，并对它们的结果进行评价，并可要求它们进行整改。它的提示词是：“你是一名广告部门的主管，负责规划和指导由用户下达的任务。对广告撰写人和美工制作的草稿和插图提出反馈意见。如果任务已完成，草稿和插图符合用户的要求，则向用户提交该结果。”

![img](https://i-blog.csdnimg.cn/img_convert/f90429cdf9f59fca1b2003240dbfb3a7.png)

##### 3、网络结构

没有组聊天结构中的监管者角色，每个agent都可以与其他agent交接。

![image-20250523091000884](/Users/shenyu/Library/Application Support/typora-user-images/image-20250523091000884.png)

## 三、常见的Agent开发框架

### （一）openai - agents- python

#### 1、概况

openai - agents - python 是一个轻量级但功能强大的框架，旨在帮助开发者构建多 Agent 的工作流程。在这个框架中，Agent 被配置为具有指令、工具、防护栏和交接机制的大型语言模型（LLMs），使得构建复杂的自动化任务和智能对话成为可能。其核心概念围绕着 Agents、Handoffs、Guardrails、Tracing 等关键组件，框架设计与支持 OpenAI Chat Completions API 格式的任何模型提供商兼容，具有高度灵活性。

#### 2、优点

1. **轻量级**：体积小巧，易于集成和部署，不会给现有系统带来负担。
2. **高度兼容**：支持与多种模型提供商的集成，确保项目的灵活性和可扩展性。
3. **内置追踪**：通过内置的 Tracing 功能，用户可以轻松追踪 Agent 的运行情况，便于调试和优化。
4. **易于上手**：项目提供了详细的文档和示例代码，帮助开发者快速入门。

#### 3、缺点

1. **没有原生多模态：**原生不提供多模态支持。
2. **Agent结构比较简单：**复杂图结构难以支持

### （二）AutoGen

#### 1、概况

AutoGen 是微软发布的一个智能体协作框架，其设计旨在帮助开发者创建基于大语言模型（LLM）的复杂应用程序，通过多个智能体之间的协作与交互，实现更加智能和灵活的任务处理。支持多智能体的编排与异步通信，还可与最先进的大型语言模型（LLM）无缝整合，并且提供了高效执行任务、调试代码以及实现智能体实时交互的工具。

#### 2、优点

1. **多智能体协作**：支持整合不同的大语言模型，允许创建代理网络，每个代理都可以独立工作或与其他代理协作。
3. **外部工具集成**：代理可以与外部工具、服务和 API 交互，显著扩展其功能。
5. **生产就绪的成熟度**：具有强大错误处理和高可靠性，确保性能稳定。
6. **工作流程的灵活性**：允许智能体间动态对话交互。
7. **支持自定义Agent结构**：提供了强大的图api，支持自定义Agent结构。
8. **跨语言支持**：支持 Python 和.NET，且计划集成更多语言，适用于多种开发生态系统。
9. **全面的社区和文档**：有丰富文档和强大社区，方便开发者获取支持和解决问题。

#### 3、缺点

1. **学习曲线陡峭**：丰富的功能集会让初学者感到不知所措。
3. **集成挑战**：与现有系统的兼容性可能需要进行自定义适配。

### （三）AgentScope

#### 1、概况

AgentScope 是阿里开源的一款全新的多智能体协同的 Multi - Agent 应用框架，旨在帮助开发者更轻松地构建基于大语言模型的多智能体应用程序。它注重易用性，为开发者提供了简洁明了的编程模式，丰富的语法工具和内置资源；集成了全面的服务级重试机制和规则性修正工具，以处理 LLMs 响应中的明显格式问题；支持多模态数据在对话呈现、消息传输和数据存储中的应用；针对分布式应用程序带来的额外编程难题和系统设计挑战，也提供了支持。

#### 2、优点

1. **易用性高**：支持纯 Python 编程，提供多种语法工具实现灵活的应用流程编排，内置丰富的 API 服务以及应用样例，供开发者直接使用，还提供了详尽的教程、API 文档和应用样例。
2. **鲁棒性强**：针对不同能力的大模型，提供了全面的重试机制、定制化的容错控制和面向 Agent 的异常处理，以确保应用的稳定、高效运行。
3. **支持多模态数据**：提供了原生的图片、音频、视频、文件消息输入，简化多模态数据的处理和展示。
4. **分布式支持好**：设计了一种分布式机制，实现了复杂分布式工作流的集中式编程和自动并行优化，用户可以使用中心化编程的方式完成分布式应用的流程编排，同时能够零代价将本地应用迁移到分布式的运行环境中。

#### 3、缺点

1. **Agent结构比较简单：**复杂图结构难以支持

### （四）LangGraph

#### 1、概况

LangGraph 是一个为开发者设计的新框架，是 LangChain 生态系统的一部分，适合那些偏好可视化方法来构建语言模型管道的用户。它允许开发者通过基于图的可视化来构建复杂的工作流，从而更容易理解不同任务和组件之间的依赖关系，这对于多个步骤（如文本生成、文档检索和分类）串联在一起的大型应用尤其有用。

#### 2、优点

1. **可视化工作流表示**：允许可视化不同组件之间的数据和操作流，这种图形化的方法直观且有助于设计更复杂的管道。
2. **调试简单**：可视化特性使得识别工作流中的瓶颈或问题节点变得更加容易。
3. **灵活性和可定制性高**：基于 LangChain 构建，采用有向循环图理念，几乎支持任何多智能体编排应用，能与开源 LLM 及各类 API 良好配合。
6. **持久性**：每个步骤后自动保存状态，确保了流程可以随时暂停或恢复执行，这对错误恢复、人力介入的工作流程以及“时间旅行”等功能至关重要。
9. **与 LangChain 集成**：无缝对接 LangChain 和 LangSmith 等生态工具，增强观测能力，但也可独立运行。

#### 3、缺点

1. **学习曲线陡峭**：上手难度大，对非程序员或编程初学者不友好，需要一定编程技能，尤其是对图和逻辑流的理解能力。

2. **预构建组件较少**：需要开发者自行实现许多功能。

3. **企业级能力需额外开发**：安全性、日志记录等企业级能力需要额外开发，文档不够详尽。

4. **没有原生多模态：**原生不提供多模态支持。

### （五）一图流总结

| 框架名              | openai-agent-python | autogen                                 | agentscope                           | langgraph |
| ------------------- | ------------------- | --------------------------------------- | ------------------------------------ | --------- |
| 开源厂商            | openai              | 微软                                    | 阿里modelscope                       | langchain |
| 上手容易程度        | 容易                | 较复杂                                  | 容易                                 | 较复杂    |
| mcp支持             | 支持☑️               | 支持☑️                                   | 支持☑️                                | 支持☑️     |
| rag支持             | 支持☑️               | 支持☑️                                   | 支持☑️                                | 不支持❌   |
| 是否支持multi-agent | 支持☑️               | 支持☑️                                   | 支持☑️                                | 支持☑️     |
| 文档完善程度        | 较完善              | 完善                                    | 完善                                 | 较完善    |
| 泛用性              | 一般                | 一般                                    | 较强                                 | 较泛用    |
| 支持模型            | openai              | anthropic、azure、llama、ollama、openai | openai、dashscope、anthropic、自定义 | 几乎所有  |
| 多模态              | 不支持❌             | 支持图片                                | 支持图片                             | 不支持❌   |
| 是否异步            | 是                  | 是                                      | 是                                   | 是        |

agentscope泛用性：较多功能使用提示词工程实现，而非通过api接口。例如mcp，结构化输出。

autogen泛用性：openaiclient主要根据openai api接口定义。涉及结构化输出功能时，deepseek api接口不兼容。有可能需要二次开发，适配特定接口。

openai-agent-python泛用性：同autogen

langgraph泛用性：功能实现依赖模型api，但是本身内置了许多模型提供商。不过langgraph不能单独拆开看，视线可以扩展到langchain生态。在生态中得以补充部分功能。比如rag。

## 四、上手实操

实践使用agentscope框架实现一个联网搜索、整理下载的multi agent应用。

支持mcp服务器提供联网搜索能力，llm模型使用deepseek。

#### 初始化deepseek client

```python
from dotenv import load_dotenv
import os
from agentscope import msghub
import agentscope
from agentscope.message import Msg
from prompts.search_agent_prompt import SEARCH_AGENT_PROMPT
from prompts.execute_command_prompt import EXECUTOR_PROMPT
from agentscope.agents import UserAgent, DialogAgent, DictDialogAgent, ReActAgent
from agentscope.parsers import MarkdownJsonDictParser
from agentscope.service import (ServiceToolkit,
                                execute_python_code,
                                execute_shell_command,
                                create_file,
                                create_directory,
                                list_directory_content,
                                get_current_directory,
                                read_text_file,
                                write_text_file,
                                )

load_dotenv()
DEEPSEEK_API_KEY = os.getenv("DEEPSEEK_API_KEY")
# config_name可以随意设置
# model_type 具体支持的值可以通过agentscope.models.model里的ModelWrapperBase实现类查看
agentscope.init(
    model_configs={
        "config_name": "deepseek-chat",
        "model_type": "openai_chat",
        "model_name": "deepseek-chat",
        "api_key": DEEPSEEK_API_KEY,
        "client_args": {
            "base_url": "https://api.deepseek.com/v1"
        }
    },
)
```

支持的模型API

| API       | 对话 | 文本生成 | 视觉 | Embedding |
| --------- | ---- | -------- | ---- | --------- |
| OpenAI    | ☑️    | ❌        | ☑️    | ☑️         |
| DashScope | ☑️    | ❌        | ☑️    | ☑️         |
| Gemini    | ☑️    | ❌        | ❌    | ☑️         |
| Ollama    | ☑️    | ☑️        | ☑️    | ☑️         |
| Yi        | ☑️    | ❌        | ❌    | ❌         |
| LiteLLM   | ☑️    | ❌        | ❌    | ❌         |
| ZhipuAI   | ☑️    | ❌        | ❌    | ☑️         |
| Anthropic | ☑️    | ❌        | ❌    | ❌         |

##### 自定义模型类

- **model_type**：在使用模型配置时，AgentScope 使用 model_type 字段来区分不同的模型 API。因此，请确保您的新模型类具有唯一的 model_type。
- **__init__**：从模型配置初始化时，AgentScope 会将配置中的所有键值对传递给模型类的 __init__ 方法。因此，请确保您的 __init__ 方法可以处理配置中的所有参数。
- **__call__**：模型类的核心方法是 __call__，它接收输入消息并返回响应。其返回值应该是 ModelResponse 对象。

```python
class MyNewModelWrapper(ModelWrapperBase):
    model_type: str = "{my_new_model_type}"

    def __init__(self, config_name, model_name, **kwargs) -> None:
        super().__init__(config_name, model_name=model_name)

        # TODO: 在这里初始化您的模型

    def __call__(self, *args, **kwargs) -> ModelResponse:
        # TODO: 在这里实现您的模型的核心逻辑

        return ModelResponse(
            text="Hello, World!",
        )
```



#### 定义一个使用mcp协议的联网搜索agent

```python
# mcp server 使用sse通信模式 
# 提前使用 mcp run main.py -t sse 启动mcp server
mcp_server_config = {
    "mcpServers": {
        "webSearch": {
            "url": "http://127.0.0.1:8000/sse",
        }
    }
}
# mcp server包装为function calling使用的tool
# model_config_name与原先定义的config_name相同
# ReActAgent 是agentscope框架本身提供的四种智能体之一
# ReActAgent 以 reasoning-acting 循环的方式使用工具来解决给定的问题。
search_toolkit = ServiceToolkit()
search_toolkit.add_mcp_servers(mcp_server_config)
search_agent = ReActAgent(
    name="web-search-assistant",
    model_config_name="deepseek-chat",
    service_toolkit=search_toolkit,
    sys_prompt=SEARCH_AGENT_PROMPT,
)
```

##### 内置智能体

| 类              | 描述                                             |
| --------------- | ------------------------------------------------ |
| UserAgent       | 允许用户参与对话的智能体。                       |
| DialogAgent     | 使用自然语言交谈的智能体。                       |
| DictDialogAgent | 支持结构化输出的智能体。                         |
| ReActAgent      | 以 reasoning-acting 循环的方式使用工具的智能体。 |
| LlamaIndexAgent | 检索增强型生成 (RAG) 智能体。                    |

每个智能体可以选择不同的大语言模型API，所有内置智能体都通过模型配置名model_config_name 来进行初始化。

#### 定义一个执行agent

```python
# agentscope本身内置了多种工具可以调用
# 可以通过 from agentscope.service import get_help 方法查看内置的工具
# mcp tool和内置tool的调用方式是一样的
executor_toolkit = ServiceToolkit()
executor_toolkit.add(execute_shell_command)
executor_toolkit.add(execute_python_code)
executor_toolkit.add(create_file)
executor_toolkit.add(create_directory)
executor_toolkit.add(list_directory_content)
executor_toolkit.add(get_current_directory)
executor_toolkit.add(read_text_file)
executor_toolkit.add(write_text_file)

executor_agent = ReActAgent(name="executor", model_config_name="deepseek-chat",
                            service_toolkit=executor_toolkit,
                            sys_prompt=EXECUTOR_PROMPT)
```

#### 定义一个路由agent

```python
# 使用结构化输出agent要求模型输出路由agent
# 结构化输出结果 msg.metadata 字段获取
# 要求模型结构化输出agentscope提供了两种方式实现
# 工具api 、 文本解析
# 这里使用的是文本解析的方式
# 更多方式可以查看 https://doc.agentscope.io/zh_CN/build_tutorial/structured_output.html#structured-output
routing_agent = DictDialogAgent(
    name="Routing",
    model_config_name="deepseek-chat",
    sys_prompt="You're a routing agent. Your target is to route the user query to the right follow-up task",
    max_retries=3
)

# 要求智能体生成包含 `thought`、`speak` 和 `decision` 的结构化输出
route_parse = MarkdownJsonDictParser(
    content_hint={
        "thought": "你的想法",
        "speak": "你要说的话",
        "decision": "你的最终决定，Search Info/Execute Command/User Interrupt/Finish",
    },
    keys_to_metadata="decision",
    required_keys=["thought", "speak", "decision"],
)
routing_agent.set_parser(route_parse)
```

#### 多agent创建聊天室

```python
# 当一个对话中有多于两个智能体时，来自一个智能体的消息应该广播给所有其他智能体。
# 为了简化广播消息的操作，AgentScope 提供了一个 msghub 模块。
# 具体来说，在同一个 msghub 中的智能体会自动接收其它参与者发送的消息。
# 我们只需要组织发言的顺序，而不需要显式地将消息传递给其它智能体。
# 在这个例子中先组织路由agent结构化输出选择下个agent
input_msg = "最近什么电影好看？整理成一个markdown文件"
msg = Msg("user", input_msg, "user")
with msghub(participants=[routing_agent, user_agent, search_agent, executor_agent], announcement=msg) as hub:
    current_step = 0
    max_step = 10
    while current_step < max_step:
        current_step += 1
        logger.info(f"step {current_step} / {max_step}")
        msg = routing_agent(msg)
        if msg.metadata == "User Interrupt":
            logger.info("decide to choose user interrupt agent")
            msg = user_agent(msg)
        if msg.metadata == "Search Info":
            logger.info("decide to choose search info agent")
            msg = search_agent(msg)
        if msg.metadata == "Execute Command":
            logger.info("decide to choose command executor agent")
            msg = executor_agent(msg)
        if msg.metadata == "Finish":
            logger.info("decide to finish task")
            break
```

#### 查看agent的记忆

```python
print("routing_agent的记忆：")
for msg in routing_agent.memory.get_memory():
    print(f"{msg.name}：{msg.content}")
```

## 五、参考列表

[agentscope项目地址](https://github.com/modelscope/agentscope)

[agentscope文档地址](https://doc.agentscope.io/zh_CN/build_tutorial/quickstart.html)

[autogen项目地址](https://github.com/microsoft/autogen)

[autogen文档地址](https://microsoft.github.io/autogen/stable/user-guide/agentchat-user-guide/installation.html)

[openai-agents-python项目地址](https://github.com/openai/openai-agents-python)

[openai-agents-python文档地址](https://openai.github.io/openai-agents-python/)

[langgraph项目地址](https://github.com/langchain-ai/langgraph)

[langgraph文档地址](https://langchain-ai.github.io/langgraph/)

[Agent进化论：大模型驱动的智能应用创新](https://blog.csdn.net/pengxin_ce/article/details/147804427)



## 六、一点玩笑话

![image-20250523152317662](/Users/shenyu/Library/Application Support/typora-user-images/image-20250523152317662.png)

创意为王、需求在先。

 形式必须服从功能，功能决定了形式。

技术服务于需求，需求决定了选型。

满足需求的情况下，选择个简单的框架，何乐不为。

