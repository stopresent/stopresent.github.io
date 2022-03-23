---
layout: single
title: "C# Rookiss Part6 웹서버 : Async, Await"
categories: WebServer
tags: WebServer
author_profile: false
sidebar:
  nav: "docs"
---


## 🙇‍♀️Async, Await


* async/await
* async 이름만 봐도.. 비동기 프로그래밍!
* 게임서버) 비동기 = 멀티쓰레드? -> 꼭 그렇지는 않습니다
* 유니티) Coroutine = 일종의 비동기 but 싱글쓰레드


### 🪐Async, Await
```
class Program
{
    static Task Test()
    {
        Console.WriteLine("start test");
        Task t = Task.Delay(3000);
        return t;
    }

    // 아이스 아메리카노를 제조중 (1분)
    // 주문 대기
    static async void TestAsync()
    {
        Console.WriteLine("start TestAsync");
        await Task.Delay(3000); // 복잡한 작업 (ex. DB나 파일 작업)
        Console.WriteLine("End TestAsync");
    }

    static void Main(string[] args)
    {
        TestAsync();

        Console.WriteLine("while start");

        while (true)
        {
            ;
        }
    }
}
```

시간이 나면 async await C#을 검색해서 ms의 예시로 공부
