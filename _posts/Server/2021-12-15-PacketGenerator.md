---
layout: single
title: "C# Rookiss Part4 ๊ฒ์์๋ฒ : PacketGenerator"
categories: Server
tags: Server
author_profile: false
sidebar:
  nav: "docs"
---


## ๐โโ๏ธPacketGenerator

ํจํท ์์ฑ๊ธฐ

### ๐ช๋ถ์

* Packet Generator 1
  * Packet class
    * Size packetId๋ ํค๋์ ๋ค์ด๊ฐ๋ ๋ถ๋ถ์ด์ง๋ง ์ค์ง์ ์ผ๋ก ์ฌ์ฉํ์ง ์์
    * Packet class๋ฅผ ์ ๊ฑฐํ๊ณ  ์์์ ์ ๊ฑฐํจ
    * ์์ฑ์๋ก ๊ฐ์ ๋ฃ๋๊ฒ์ด ์๋๋ผ ๋ง๋ฐ๋ก ๋ฃ์
  * PacketGenerator
    * ์๋ณธ์ ๋ํ ์ ์๊ฐ ํ์
    * ํจํท ์ ์๋ฅผ ์ด๋ค ๋ฐฉ์์ผ๋ก ํ ์ง ์ ์ ํด์ผ๋๋ค (json, xml)
  * PDL(PacketDefinitionList) - xml
    * ํค๋์ xml์๋ํ ์ ๋ณด๊ฐ ๋ค์ด๊ฐ
    * ์์ผ๋ก ์ด๋ฃจ์ด์ ธ ์๋ค - `<PDL>  </PDL>`
    * `<long name="playerId"/>` ์ด๋ฐ์์ผ๋ก ๋ฐ๋ก ๋ซ์์ ์๋ค
    * packet long string ๋ฑ๋ฑ์ ์ ํด์ง ์ด๋ฆ์ด ์๋๋ผ ํ์ฑ์ ์ฌ์ฉํ๊ธฐ ์ํด์ ๋ง์ถฐ์ฃผ๋ ์ด๋ฆ์ด๋ค
    * ๊ธฐ๋ณธ์ ์ธ๊ฒ๋ค์ ์๋ฉด ์ ์ฒด์ ์ธ ๊ตฌ์กฐ๋ฅผ ์์ ์๊ณ  ์ด๋ฅผ ์ด์ฉํด ์๋ํ ํด์ ๋ง๋ ๋ ๊ฒ!
    * xml์ ๋์นญ์ ์ธ ๊ตฌ์กฐ์์ ํธํ๊ณ  ์ด์ค ๋ฆฌ์คํธ๊ฐ ๊ฐ๋ฅํ๋ค
    * cpp์์๋ ๊ณต์๋ผ์ด๋ธ๋ฌ๋ฆฌ ์์ xml์ ํ์ฑํ๋ ๊ฒ์ด ์์ง๋ง cs์๋ ์์ (XmlReader)


```cs
<?xml version="1.0" encoding="utf-8" ?>
<PDL>
	<packet name="C_PlayerInfoReq">
		<byte name="testByte"/>
		<long name="playerId"/>
		<string name="name"/>
		<list name="skill">
			<int name="id"/>
			<short name="level"/>
			<float name="duration"/>
			<list name="attribute">
				<int name="att"/>
			</list>
		</list>
	</packet>
	<packet name ="S_Text">
		<int name="testInt"/>
	</packet>
</PDL>
```

* XmlReader
  * PacketGenerator Program
    * XmlReader๋ก Xml๋ฅผ ์ฝ์ ์ ์๊ณ  Create๋ก ํ์ผ๊ณผ ์ต์์ ๋ฃ์ ์ ์๋ค
    * `XmlReader r = XmlReader.Create("PDL.xml", settings);`
    * ์ด๋ settings๋ XmlReaderSettings๋ฅผ ๋ฐํํ๋๋ฐ ์จ๊ฐ ์ค์ ์ด ๊ฐ๋ฅ!
    * `IgnoreComments = true` - ์ฃผ์์ ๋ฌด์
    * `IgnoreWhitespace = true` - ์คํ์ด์ค๋ฅผ ๋ฌด์


    ```cs
     XmlReaderSettings settings = new XmlReaderSettings()
    {
        IgnoreComments = true,
        IgnoreWhitespace = true
    };
    ```


    * XmlReader๊ฐ ํ์ฑ์ ์๋ฃํ ๋ค์๋ `r.Dispose();` ๋ก ๋ซ์์ฃผ๋ ์์์ ํด์ผ๋จ
    * using() {}์ ์ฌ์ฉํ๋ฉด ๋ง์น  ๋ ์๋์ผ๋ก Dispose()๋ฅผ ํด์ค
    * `r.MoveToContent();`๋ฅผ ํ๋ฉด PDL์์ ํค๋๊ฐ์ ๋ถ๋ถ์ ๊ฑด๋๋ฐ๊ณ  ํต์ฌ์ ์ด ๋ด์ฉ์ผ๋ก ๋ฐ๋ก ๊ฐ
    * `r.Read()`๋ ํ์ฑ์ ํ๋ ๊ฒ์ผ๋ก Stream๋ฐฉ์ ์ฆ, ํ์คํ์ค ์ฝ์


    ```cs
    using (XmlReader r = XmlReader.Create(pdlPath, settings))
    {
        r.MoveToContent();

        while (r.Read())
        {
            Console.WriteLine(r.Name + " " + r["name"]);
        }
    }
    ```


    * `Console.WriteLine(r.Name + " " + r["name"]);` - `r["name"]์ name์ด๋ผ๋ ํ๋กํผํฐ๋ฅผ ์ฝ๋ ๊ฒ
    * `r.Name` - packet (ํ์) , `r["name"]` - PlayerInfoReq (๋ด์ฉ๋ฌผ)
    * `</packet> </PDL>`๋ ํ์ฑ์ํจ - `pakcet PDL`

    * ๋ฐ๋ก PacektGenerator๋ฅผ ๋น๋ํ๋ฉด ์๋ฌ๊ฐ ๋จ
    * ์คํํ์ผ์ด ์๋ ์์น์์ ์ฝ๊ธฐ ๋๋ฌธ์ PDLํ์ผ์ ์คํํ์ผ์ด ์๋ ํด๋์ ๋ฃ์ด์ฃผ์ด์ผ ๋จ!

```cs
static void Main(string[] args)
{
    XmlReaderSettings settings = new XmlReaderSettings()
    {
        IgnoreComments = true,
        IgnoreWhitespace = true
    };

    using (XmlReader r = XmlReader.Create(settings))
    {
        r.MoveToContent();

        while (r.Read())
        {
            if (r.Depth == 1 && r.NodeType == XmlNodeType.Element)
            {
                ParsePacket(r);
            }
            //Console.WriteLine(r.Name + " " + r["name"]);
        }
    }
}
```

* packet์ฝ๊ธฐ

`if(r.Depth == 1 && r.NodeType == XmlNodeType.Element)`๋ฉด `ParsePacket(r);`์ ์คํ
`r.Depth`๋ PDL์ packet์ ๊น์ด๊ณ  ` r.NodeType == XmlNodeType.Element`๋ถ๋ถ์ `</packet> </PDL>`์ ํ์ฑํ์ง ์๊ฒ ์์ํ๋ ๋ถ๋ถ๋ง ์ฝ์

* ParsePacket

```cs
public static void ParsePacket(XmlReader r)
{
    if (r.NodeType == XmlNodeType.EndElement)
        return;

    if (r.Name.ToLower() != "packet")
    {
        Console.WriteLine("Invalid packet node");
        return;
    }

    string packetName = r["name"];
    if (string.IsNullOrEmpty(packetName))
    {
        Console.WriteLine("Packet without name");
        return;
    }
    
    ParseMembers(r);
}
```

  * `if (r.NodeType == XmlNodeType.EndElement) return;`์ผ๋ก ๋ซํ๋ ๋ถ๋ถ์ด๋ฉด return
  * `if (r.Name.ToLower() != "packet") { Console.WriteLine("Invalid packet node"); return; }`์ผ๋ก packet๊ณผ ์ด๋ฆ์ด ๋ค๋ฅด๋ฉด return, `r.Name.ToLower()`์ `r.Name`์ ์๋ฌธ์๋ก ๋ฐ๊ฟ
  * `string packetName = r["name"];`๋ก packet์ ํ๋กํผํฐ๋ฅผ ๊ฐ์ ธ์ด
  * `if (string.IsNullOrEmpty(packetName)) { Console.WriteLine("Packet without name"); return; }`๋ก ๋น์ด์๋ค๋ฉด return

์ฌ๊ธฐ๊น์ง ์ค๋ฉด packet์ธ๊ฒ์ด ๋ถ๋ชํ๋ packet๋ด์ Member๋ค์ ์ฝ์ด์ผํ๋ฏ๋ก ParseMember(r)๋ก ์ด๋


* ParseMembers

```cs
public static void ParseMembers(XmlReader r)
{
    string packetName = r["name"];

    int depth = r.Depth + 1;
    while (r.Read())
    {
        if (r.Depth != depth)
            break;

        string memberName = r["name"];
        if (string.IsNullOrEmpty(memberName))
        {
            Console.WriteLine("Member without name");
            return;
        }

        string memberType = r.Name.ToLower();
        switch (memberType)
        {
            case "byte":
            case "sbyte":
            case "bool":
            case "short":
            case "ushort":
            case "int":
            case "long":
            case "float":
            case "double":
            case "string":
            case "list":
            default:
                break;
        }
    }
}
```

`int depth = r.Depth + 1;`

์ฆ depth๊ฐ +1 ์ผ ๋๊น์ง ๋ฃจํ๋ฅผ ๋๋๊น
```
<int name="id"/>
<short name="level"/>
<float name="duration"/>
```
๋ฅผ ๋ค ํ์ฑํ๊ณ  ๋น ์ ธ๋์ด

`string memberType = r.Name.ToLower();`๋ก ๋งด๋ฒ๋ค์ ํ์์ ์ฒดํฌ

์ค์์น๋ฌธ์ ๋๋ฉด์ ๊ฐ ํ์๋ง๋ค ์ด๋ค ๊ฐ์ ๋ฃ์์ง๋ฅผ ์ ํจ

### ๐ชPacketFormat


`public static string packetFormat = @"";` ํ์

@"";์ ์ฌ๋ฌ์ค์ ๋ฌธ์์ด์ ๋ฃ์ ๋ ์ฌ์ฉํจ

๊ณ ์ ์ ์ธ๊ฒ์ ๊ทธ๋๋ก ๋๋๊ณ  ๊ฐ๋ณ์ ์ธ ๊ฒ๋ค์ {0} {1} {2} ... ์์ผ๋ก ๊ต์ฒดํ๋ค

* ๊ฐ๋ณ์ ์ธ ๊ฒ๋ค
  * ํ์ฑํ xml์์ ๋ด์ฉ์ด ๋ค์ด๊ฐ์ผ ํ๋ ๊ฒ๋ค - ๋ฉค๋ฒ ๋ณ์๋ค
  * ํจํท ์ด๋ฆ๋ฑ PDL์ ์ ์๋ ๊ฒ๋ค - ํจํท ์ด๋ฆ
  * ์ค์ง์ ์ผ๋ก ๋ฐ์ดํฐ๊ฐ ๋ค์ด๊ฐ๋ ๋ถ๋ถ - ๋ฉค๋ฒ ๋ณ์ Read
  * ๋ณ์ ํ์, ๋ณ์ ์ด๋ฆ ๋ฑ๋ฑ









