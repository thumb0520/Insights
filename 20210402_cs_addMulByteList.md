# 拼接多个ByteList

~~~cs
    public byte[] AddByte(params byte[][] byteList)
    {
        List<byte> tempList = new List<byte>();
        foreach (byte[] b in byteList)
        {
            tempList.AddRange(b);
        }
        return tempList.ToArray();
    }
~~~
