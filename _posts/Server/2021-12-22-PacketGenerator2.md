---
layout: single
title: "C# Rookiss Part4 게임서버 : PacketGenerator2"
categories: Server
tags: Server
author_profile: false
sidebar:
  nav: "docs"
---

## 🙇‍♀️PacketGenerator2

* PacketGenerator1의 활동
  * PDL(PacketDefinitionList) 생성
  * XmlReader를 통해 PDL을 파싱
  * packet과 packet의 memeber들을 순차적으로 파싱
  * 자동화 툴을 만들기 위하여 PacketFormat class 생성
  * public static string으로 여러 문자열(@"";)을 Session의 Read, Write 등 클래스 복사
  * 고정적인 부분은 그대로 가변적인 부분은 {0} {1} ...로 치환


* PacketGenerator2에서 할 것들
  * genPakcets라는 string을 만들어서 총 함쳐진 gemPackets.cs파일의 내용을 담을 것
  * PacketFormat에서 만든 {0} {1} .. 을 채워주기 위해 변수를 만들어서 밀어넣어주고 합체
  * List부분도 만들어줘서 memberListFormat, readListFormat, writeListFormat 만들어 주기

### 🪐차근차근

* PacketGenerator Program
  * `File.WriteAllText("GenPackets.cs",genPackets);`로 하나의 파일로 만들어 줌
    * 첫 인자는 파일 이름, 다음 인자는 자동완성 한 것들을 넣어줌
  * `static string genPackets;` - 자동완성한것을 여기에 밀어 넣어 줄거임
    * ParsePacket을 하는 동안에 genPackets에 넣을 것을 완성해야 됨
    * ParsePacket속 ParseMembers가 끝나면 `genPackets += string.Format(PacketFormat.packetFormat, "", "", "", "")`로 내용을 밀어 넣음
      * 이때 "" 은 가변인자로 {0} {1} 로 바꾼 부분들
      * 각각 패킷 이름, 멤버 변수들, 멤버 변수 Read, 멤버 변수 Write 이고 패킷이름은 packetName으로 대응됨
      * 나머지는 ParseMember를 void에서 Tuple<string, string, string>으로 바꾸어 반환하도록 만들 후 대입 - 다 멤버 변수들이니깐
      * Tuple<string, string, string> t = ParseMemebers(r);로 ParsePacket에서 받고 멤버 변수들, 멤버 변수 Read, 멤버 변수 Write를 각각 t.Item1, t.Item2, t.Item3로 대입
    * `genPackets += string.Format(PacketFormat.packetFormat, packetName, t.Item1, t.Item2, t.Item3)`로 변환 됨
* ParseMember
  * `string memberCode = ""; string readCode = ""; string writeCode = "";`를 넣어주고 while(r.Read())를 돌면 `Tuple<string, string, string>`을 밷어주게 만듦
  * r.Read()를 한다는 것은 PDL을 한줄한줄씩 긁어오는 것이므로 `if(string.IsNullOrEmpty(memberCode) == false) memberCode += Environment.NewLine;`으로 Enter를 쳐줌
  * `if(string.IsNullOrEmpty(readCode) == false) readCode += Environment.NewLine;` `if(string.IsNullOrEmpty(writeCode) == false) writeCode += Environment.NewLine;`도 추가

```cs
string memberType = r.Name.ToLower();
switch (memberType)
{
    case "byte":
    case "sbyte":
        memberCode += string.Format(PacketFormat.memberFormat, memberType, memberName);
        readCode += string.Format(PacketFormat.readByteFormat, memberName, memberType);
        writeCode += string.Format(PacketFormat.writeByteFormat, memberName, memberType);
        break;
    case "bool":
    case "short":
    case "ushort":
    case "int":
    case "long":
    case "float":
    case "double":
        memberCode += string.Format(PacketFormat.memberFormat, memberType, memberName);
        readCode += string.Format(PacketFormat.readFormat, memberName, ToMemberType(memberType), memberType);
        writeCode += string.Format(PacketFormat.writeFormat, memberName, memberType);
        break;
    case "string":
        memberCode += string.Format(PacketFormat.memberFormat, memberType, memberName);
        readCode += string.Format(PacketFormat.readStringFormat, memberName);
        writeCode += string.Format(PacketFormat.writeStringFormat, memberName);
        break;
    case "list":
        Tuple<string, string, string> t = ParseList(r);
        memberCode += t.Item1;
        readCode += t.Item2;
        writeCode += t.Item3;
        break;
    default:
        break;
}
```

PDL을 한줄한줄 파싱하면서 긁은후 memberCode, readCode, writeCode를 덧붙여서 완성을 한후 Tuple로 전달해 주는 부분


```cs
memberCode = memberCode.Replace("\n", "\n\t");
readCode = readCode.Replace("\n", "\n\t\t");
writeCode = writeCode.Replace("\n", "\n\t\t");
```

정렬을 맞춰주는 것으로 `memberCode.Replace("\n", "\n\t");`은 엔터가 있는 애들은 엔터후 탭을 눌러주세요라는 뜻



그후 List도 같은 작업..



### 🪐분석 및 작동파악하기

일단 PDL.xml을 만들었고 PacketGenerator의 Program에서 XmlReader를 통해 PDL을 파싱

Program에서 ParsePacket과 ParseMembers를 통해 genPackets의 내용물을 완성 후 File.WriteAllText("GenPackets.cs", genPackets);로 파일 생성



xml은 원본 데이터의 대략적인 구조를 알게해주는 것

구조를 알았으니 xml을 파싱을해서 원본 데이터를 자동 생성하는 툴을 만들면 된다!!
xml 파싱은 XmlReader의 Read()로 파싱을 하는 것이고 파싱의 내용은 r.Name과 r["name"]이 있다
각각 타입과 name에 들어간 값이 나온다

xml을 파싱하는데 packet 부분과 member부분으로 나뉜다
- 여러가지 패킷이 있으므로 한패킷당 class로 만들기 위함이라 생각된다 (class가 아니더라고 구분해주는 역할이라고 생각하면 될거같다)

그렇다는 것은 parsePacket에 들어오면 그 패킷안의 members를 파싱을 해야 된다는 것!

ParseMemebers로 r.Depth + 1인 부분들만 파싱을하여 Packet내의 멤버들만 파싱을 하게 한다

멤버들의 r["name"]과 r.Name을 파싱을하고 타입에 따라 자동화 되는 부분을 만들어 주면 된다
- 타입이라하면 xml을 통해 알수있는 원본 데이터의 틀이다
- 그 틀에서 자동화 되는 부분을 집어 넣으면 원본과 같아지는 것이다 

각 타입 케이스마다 값을 넣어줘야 하는데 이때 PacketFormat을 사용하는 것이다
PacketFormat 파일을 만들어서 자동화할 내용을 바꿔치기 하는 것!!

각각의 타입에 맞게 내용이 바뀌니 그거에 맞춰서 memberCode, readCode, writeCode를 만들어 밀어 넣어준다

모든 케이스를 돌고나서 완성된 큰~~~ 문자열은 WriteAllText를 통해 cs파일로 만든다

자동화 끝.











