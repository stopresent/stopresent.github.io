---
layout: single
title: "C# Rookiss Part4 게임서버 : Connector"
categories: Server
tags: Server
author_profile: false
sidebar:
  nav: "docs"
---


## 🙇‍♀️Connector

리스너와 반대되는 개념

### 🪐코드

```cs
public class Connector
{
    Func<Session> _sessionFactory;

    public void Connect(IPEndPoint endPoint, Func<Session> sessionFactory)
    {
        // 휴대폰 설정
        Socket socket = new Socket(endPoint.AddressFamily, SocketType.Stream, ProtocolType.Tcp);
        _sessionFactory = sessionFactory;
        SocketAsyncEventArgs args = new SocketAsyncEventArgs();
        args.Completed += OnConnectCompleted;
        args.RemoteEndPoint = endPoint;
        args.UserToken = socket;

        RegisterConnect(args);
    }

    void RegisterConnect(SocketAsyncEventArgs args)
    {
        Socket socket = args.UserToken as Socket;
        if (socket == null)
            return;

        bool pending = socket.ConnectAsync(args);
        if (pending == false)
            OnConnectCompleted(null, args);
    }

    void OnConnectCompleted(object sender, SocketAsyncEventArgs args)
    {
        if (args.SocketError == SocketError.Success)
        {
            Session session = _sessionFactory.Invoke();
            session.Start(args.ConnectSocket);
            session.OnConnected(args.RemoteEndPoint);
        }
        else
        {
            Console.WriteLine($"OnConnectCompleted Fail : {args.SocketError}");
        }
    }
}
```

### 🪐분석

커넥터를 만드는 이유 : 리시브랑 샌드를 공용으로 합치기 위해, 분할 서버를 할 때 통신을 하기 위해 (한쪽은 리스터 상태, 한쪽은 커넥트 상태 )

연결하는 부분은 상대방의 주소를 넣어줘야 됨 - args.RemoteEndPoint = endPoint;

Socket을 맴버변수로 두지 않는 이유 : 커넥터를 한명만 받고 끝내는게 아니라 여러명을 받을 수 있기 때문에 이벤트를 통해서 인자를 넘겨줌

Session.Start(args.ConnectSocket) - Session.Start를 하기 위해 소켓이 필요한데 args.ConnectSocket로 넣어줌, Start를 했으면 Recv이벤트는 등록된 상태

서버코어에 있는 커넥터 등등의 함수를 클라랑 서버에서 사용하기 위해 서버코어를 라이브러리로 만들기

### 🪐라이브러리

ServerCore 속성 -> 출력형식 -> 클래스라이브러리
Server, DummyClient 추가 -> 참조 -> ServerCore선택
