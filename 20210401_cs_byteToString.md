# byte[] 转 string
## ASCII 编码的Byte数组转为string
~~~cs
    using System;   
    using System.Text;   
     
    public static string FromASCIIByteArray(byte[] characters)   
    {   
        ASCIIEncoding encoding = new ASCIIEncoding( );   
        string constructedString = encoding.GetString(characters);   
        return (constructedString);   
    }
~~~
## Unicode 编码的Byte数组转为string
~~~cs
    public static string FromUnicodeByteArray(byte[] characters)   
    {   
        UnicodeEncoding encoding = new UnicodeEncoding( );   
        string constructedString = encoding.GetString(characters);   
        return (constructedString);   
    }  
~~~

# string 转 byte[]
~~~cs
    public static byte[] StringToBytelist(string str)
    {
        return System.Text.Encoding.UTF8.GetBytes(json);
    }
~~~