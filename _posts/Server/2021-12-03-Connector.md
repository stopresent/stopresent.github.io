---
layout: single
title: "C# Rookiss Part4 ๊ฒ์์๋ฒ : Connector"
categories: Server
tags: Server
author_profile: false
sidebar:
  nav: "docs"
---


## ๐โโ๏ธConnector

๋ฆฌ์ค๋์ ๋ฐ๋๋๋ ๊ฐ๋

### ๐ช์ฝ๋

```cs
public class Connector
{
    Func<Session> _sessionFactory;

    public void Connect(IPEndPoint endPoint, Func<Session> sessionFactory)
    {
        // ํด๋ํฐ ์ค์ 
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

### ๐ช๋ถ์

์ปค๋ฅํฐ๋ฅผ ๋ง๋๋ ์ด์  : ๋ฆฌ์๋ธ๋ ์๋๋ฅผ ๊ณต์ฉ์ผ๋ก ํฉ์น๊ธฐ ์ํด, ๋ถํ  ์๋ฒ๋ฅผ ํ  ๋ ํต์ ์ ํ๊ธฐ ์ํด (ํ์ชฝ์ ๋ฆฌ์คํฐ ์ํ, ํ์ชฝ์ ์ปค๋ฅํธ ์ํ )

์ฐ๊ฒฐํ๋ ๋ถ๋ถ์ ์๋๋ฐฉ์ ์ฃผ์๋ฅผ ๋ฃ์ด์ค์ผ ๋จ - args.RemoteEndPoint = endPoint;

Socket์ ๋งด๋ฒ๋ณ์๋ก ๋์ง ์๋ ์ด์  : ์ปค๋ฅํฐ๋ฅผ ํ๋ช๋ง ๋ฐ๊ณ  ๋๋ด๋๊ฒ ์๋๋ผ ์ฌ๋ฌ๋ช์ ๋ฐ์ ์ ์๊ธฐ ๋๋ฌธ์ ์ด๋ฒคํธ๋ฅผ ํตํด์ ์ธ์๋ฅผ ๋๊ฒจ์ค

Session.Start(args.ConnectSocket) - Session.Start๋ฅผ ํ๊ธฐ ์ํด ์์ผ์ด ํ์ํ๋ฐ args.ConnectSocket๋ก ๋ฃ์ด์ค, Start๋ฅผ ํ์ผ๋ฉด Recv์ด๋ฒคํธ๋ ๋ฑ๋ก๋ ์ํ

์๋ฒ์ฝ์ด์ ์๋ ์ปค๋ฅํฐ ๋ฑ๋ฑ์ ํจ์๋ฅผ ํด๋ผ๋ ์๋ฒ์์ ์ฌ์ฉํ๊ธฐ ์ํด ์๋ฒ์ฝ์ด๋ฅผ ๋ผ์ด๋ธ๋ฌ๋ฆฌ๋ก ๋ง๋ค๊ธฐ

### ๐ช๋ผ์ด๋ธ๋ฌ๋ฆฌ

ServerCore ์์ฑ -> ์ถ๋ ฅํ์ -> ํด๋์ค๋ผ์ด๋ธ๋ฌ๋ฆฌ
Server, DummyClient ์ถ๊ฐ -> ์ฐธ์กฐ -> ServerCore์ ํ
