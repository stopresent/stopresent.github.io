---
layout: single
title: "PacketGenerator"
categories: Server
tags: Server
author_profile: false
sidebar:
  nav: "docs"
---


## 🙇‍♀️PacketGenerator

패킷 생성기

### 🪐분석

* Packet Generator 1
  * Packet class
    * Size packetId는 헤더에 들어가는 부분이지만 실질적으로 사용하지 않음
    * Packet class를 제거하고 상속을 제거함
    * 생성자로 값을 넣는것이 아니라 막바로 넣음
  * PacketGenerator
    * 원본에 대한 정의가 필요
    * 패킷 정의를 어떤 방식으로 할지 정의 해야된다 (json, xml)
  * PDL(PacketDefinitionList) - xml
    * 헤더에 xml에대한 정보가 들어감
    * 쌍으로 이루어져 있다 - `<PDL>  </PDL>`
    * `<long name="playerId"/>` 이런식으로 바로 닫을수 있다
    * packet long string 등등은 정해진 이름이 아니라 파싱을 사용하기 위해서 맞춰주는 이름이다
    * 기본적인것들을 알면 전체적인 구조를 알수 잇고 이를 이용해 자동화 툴을 만든는 것!
    * xml은 대칭적인 구조에서 편하고 이중 리스트가 가능하다
    * cpp에서는 공식라이브러리 상에 xml을 파싱하는 것이 없지만 cs에는 있음 (XmlReader)


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
    * XmlReader로 Xml를 읽을 수 있고 Create로 파일과 옵션을 넣을 수 있다
    * `XmlReader r = XmlReader.Create("PDL.xml", settings);`
    * 이때 settings는 XmlReaderSettings를 반환하는데 온갖 설정이 가능!
    * `IgnoreComments = true` - 주석을 무시
    * `IgnoreWhitespace = true` - 스페이스를 무시


    ```cs
     XmlReaderSettings settings = new XmlReaderSettings()
    {
        IgnoreComments = true,
        IgnoreWhitespace = true
    };
    ```


    * XmlReader가 파싱을 완료한 뒤에는 `r.Dispose();` 로 닫아주는 작업을 해야됨
    * using() {}을 사용하면 마칠 때 자동으로 Dispose()를 해줌
    * `r.MoveToContent();`를 하면 PDL에서 헤더같은 부분은 건너뛰고 핵심적이 내용으로 바로 감
    * `r.Read()`는 파싱을 하는 것으로 Stream방식 즉, 한줄한줄 읽음


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


    * `Console.WriteLine(r.Name + " " + r["name"]);` - `r["name"]은 name이라는 프로퍼티를 읽는 것
    * `r.Name` - packet (타입) , `r["name"]` - PlayerInfoReq (내용물)
    * `</packet> </PDL>`도 파싱을함 - `pakcet PDL`

    * 바로 PacektGenerator를 빌드하면 에러가 남
    * 실행파일이 있는 위치에서 읽기 때문에 PDL파일을 실행파일이 있는 폴더에 넣어주어야 됨!

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

* packet읽기

`if(r.Depth == 1 && r.NodeType == XmlNodeType.Element)`면 `ParsePacket(r);`을 실행
`r.Depth`는 PDL에 packet의 깊이고 ` r.NodeType == XmlNodeType.Element`부분은 `</packet> </PDL>`을 파싱하지 않게 시작하는 부분만 읽음

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

  * `if (r.NodeType == XmlNodeType.EndElement) return;`으로 닫히는 부분이면 return
  * `if (r.Name.ToLower() != "packet") { Console.WriteLine("Invalid packet node"); return; }`으로 packet과 이름이 다르면 return, `r.Name.ToLower()`은 `r.Name`을 소문자로 바꿈
  * `string packetName = r["name"];`로 packet의 프로퍼티를 가져옴
  * `if (string.IsNullOrEmpty(packetName)) { Console.WriteLine("Packet without name"); return; }`로 비어있다면 return

여기까지 오면 packet인것이 분명하니 packet내의 Member들을 읽어야하므로 ParseMember(r)로 이동


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

즉 depth가 +1 일 때까지 루프를 도니까
```
<int name="id"/>
<short name="level"/>
<float name="duration"/>
```
를 다 파싱하고 빠져나옴

`string memberType = r.Name.ToLower();`로 맴버들의 타입을 체크

스위치문을 돌면서 각 타입마다 어떤 값을 넣을지를 정함

### 🪐PacketFormat


`public static string packetFormat = @"";` 형식

@"";은 여러줄의 문자열을 넣을 때 사용함

고정적인것은 그대로 놔두고 가변적인 것들은 {0} {1} {2} ... 순으로 교체한다

* 가변적인 것들
  * 파싱한 xml에서 내용이 들어가야 하는 것들 - 멤버 변수들
  * 패킷 이름등 PDL에 정의된 것들 - 패킷 이름
  * 실질적으로 데이터가 들어가는 부분 - 멤버 변수 Read
  * 변수 형식, 변수 이름 등등









