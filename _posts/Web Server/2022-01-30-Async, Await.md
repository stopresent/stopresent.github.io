---
layout: single
title: "C# Rookiss Part6 ์น์๋ฒ : Async, Await"
categories: WebServer
tags: WebServer
author_profile: false
sidebar:
  nav: "docs"
---


## ๐โโ๏ธAsync, Await


* async/await
* async ์ด๋ฆ๋ง ๋ด๋.. ๋น๋๊ธฐ ํ๋ก๊ทธ๋๋ฐ!
* ๊ฒ์์๋ฒ) ๋น๋๊ธฐ = ๋ฉํฐ์ฐ๋ ๋? -> ๊ผญ ๊ทธ๋ ์ง๋ ์์ต๋๋ค
* ์ ๋ํฐ) Coroutine = ์ผ์ข์ ๋น๋๊ธฐ but ์ฑ๊ธ์ฐ๋ ๋


### ๐ชAsync, Await
```
class Program
{
    static Task Test()
    {
        Console.WriteLine("start test");
        Task t = Task.Delay(3000);
        return t;
    }

    // ์์ด์ค ์๋ฉ๋ฆฌ์นด๋ธ๋ฅผ ์ ์กฐ์ค (1๋ถ)
    // ์ฃผ๋ฌธ ๋๊ธฐ
    static async void TestAsync()
    {
        Console.WriteLine("start TestAsync");
        await Task.Delay(3000); // ๋ณต์กํ ์์ (ex. DB๋ ํ์ผ ์์)
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

์๊ฐ์ด ๋๋ฉด async await C#์ ๊ฒ์ํด์ ms์ ์์๋ก ๊ณต๋ถ
