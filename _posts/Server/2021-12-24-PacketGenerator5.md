---
layout: single
title: "C# Rookiss Part4 게임서버 : PacketGenerator5"
categories: Server
tags: Server
author_profile: false
sidebar:
  nav: "docs"
---


### 🙇‍♀️PacketGanerator5


ClientSession에서 OnRecvPacket단계에 패킷을 받아서 조립을 한 후 그 다음 작업을 switch로 넣었다

규모가 커지면 프로토콜에 패킷이 엄청 늘어나게 되는데 이때 case가 너무 많아지면 비효율적이니 이 부분을 개선해보자

직접 케이스문을 만들어 준 후 파싱하는 부분을 넣어주는 작업을 자동화 하기


### 🪐차근차근


switch, case문을 자동화하기 위해 PalyerInfoReq의 부모 클래스(Interface)만들기

IPacket에는 `ushort Protocol { get; }` `Read` `Write` 를 넣어줌
Protocol은 PlayerInfoReq기준으로 `public ushort Protocol { get { return (ushort)PacketID.PlayerInfoReq; } }`로 구현된다

위에 부분들은 PacketFormat에 자동화 코드를 추가 해준다




### 🪐PacketHandler, PacketManager


PacketHandler에서는 `PlayerInfoReq q = packet as PlayerInfoReq;`로 캐스팅해주고
case문에서 작동하던 작업을 옮겨준다

호출하는 부분을 자동화 할거임


PacketManager는 싱글톤으로 만들고

```cs
#region Singleton
static PacketManager _instance;
public static PacketManager Instance
{
    get
    {
        if (_instance == null)
            _instance = new PacketManager();
        return _instance;
    }
}
#endregion
```

OnRecvPacket을 만들어서 기존에 ClientSession의 OnRecvPakcet의 내용물을 옮긴다(switch case까지)

매니저를 호출하는 식으로 바뀜



### 🪐자동화 시작

* switch, case문을 자동으로 등록하는 작업

PakcetHandler, ClientPacketManager, ServerPacketManager를 호출하는 방법








