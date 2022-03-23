---
layout: single
title: "C# Rookiss Part4 게임서버 : PacketGenerator3"
categories: Server
tags: Server
author_profile: false
sidebar:
  nav: "docs"
---

## 🙇‍♀️PacketGenerator3


byte계열의 변수들은 BitConverter를 사용하지 않기 때문에 자동화 코드 추가

### 🪐차근차근


GenPackets의 내용을 수동으로 복붙하는게 아니라 파일자체를 클라나 서버가 참조하게 끔 만들어야 됨

enum값과 using시리즈들이 빠졌기때문에 추가해주는 작업이 필요 - 파일 자체에 대한 포멧을 만들기 (fileFormat)
genPakcets로 File.WriteAllText를 하는게 아니라 한단계 건너서 만들게 작동

enum packeId을 담을 멤버변수 pakcetEnum을 만들고 패킷이름/번호를 받기에 ushort타입의 멤버변수 packetId를 추가한후 
genPackets와 마찬가지로 `packetEnums += string.Format(PacketFormat.packetEnumFormat, packetName, ++packetId);`를 ParsePacket에 추가
- 엔터키와 탭을 추가하기 위해 `+ Environment.NewLine + "\t";`을 더해줌 


* byte계열의 변수들의 자동화 코드
  * byte들은 인덱스를 이용하면 됨
  * read
    * `this.testbyte = segment.Array[segment.Offset + count];`
    * `count += sizeof(byte);`
  * write
    * `segment.Array[segment.Offset + count] = this.testByte;`
    * `count += sizeof(byte);


### 🪐
