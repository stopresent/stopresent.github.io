---
layout: single
title: "C# Rookiss Part4 ๊ฒ์์๋ฒ : ThreadLocalStorage"
categories: Server
tags: Server
author_profile: false
sidebar:
  nav: "docs"
---


## ๐โโ๏ธTLS

* ์ ์ญ๋ณ์์ธ๋ฐ ์ฐ๋ ๋๋ง๋ค ๊ณ ์ ํ๊ฒ ์ ๊ทผํ ์์๋ ์ ์ญ๋ณ์
- ์ง์์๊ฒ ํ๊บผ๋ฒ์ ์ผ์ํค๊ธฐ (ํฐ ์๋ฐ์ ๋ฐ์ฐฌ, ๋ฉ์ธ ๋ฉ๋ด ๋ฑ๋ฑ์ ์ฌ๋ ค์ ์๋น)
- ์คํ์ ๋ฃ๊ณ ์ถ์ง๋ง ์คํ์ ๋ถ์์ ํจ
- TLS๋ฅผ ๋ง๋ค์ด์ ์ฌ์ฉ!!


### ๐ชTLS

```cs
class Program
{
    static ThreadLocal<string> ThreadName = new ThreadLocal<string>();

    static void WhoAmI()
    {
        ThreadName.Value = $"ThreadName is {Thread.CurrentThread.ManagedThreadId}";

        Thread.Sleep(100);

        Console.WriteLine(ThreadName.Value);
    }

    static void Main(string[] args)
    {
        // ThreadLocalStorage
        // ์ ์ญ๋ณ์์ธ๋ฐ ์ฐ๋ ๋๋ง๋ค ๊ณ ์ ํ๊ฒ ์ ๊ทผํ ์์๋ ์ ์ญ๋ณ์
        Parallel.Invoke(WhoAmI, WhoAmI, WhoAmI, WhoAmI, WhoAmI, WhoAmI);
    }
}
```

Parallel.Invoke() ์์ ๋ค์ด๊ฐ Action๋งํผ ํ์คํฌ๋ฅผ ๋ง๋ค์ด ์คํ ์์ผ์ค
์ฐ๋ ๋ ํ์์ ๋ง๋ค์ด์ง

* ๋ฌธ์ ์ 
  * ์ผ๊พผ์ด ์ผํ๋ฌ ๊ฐ๋ค์ค๋ฉด ๊ฐ์ ๋ฎ์ด์จ์ ๋ค์ ์ผํจ

### ๐ชTLS repeat

```cs
class Program
{
    static ThreadLocal<string> ThreadName = new ThreadLocal<string>(() => { return $"ThreadName is {Thread.CurrentThread.ManagedThreadId}"; });

    static void WhoAmI()
    {
        bool repeat = ThreadName.IsValueCreated;
        if (repeat)
            Console.WriteLine(ThreadName.Value + " (repeat)");
        else
            Console.WriteLine(ThreadName.Value);
    }

    static void Main(string[] args)
    {
        ThreadPool.SetMinThreads(1, 1);
        ThreadPool.SetMaxThreads(3, 3);
        Parallel.Invoke(WhoAmI, WhoAmI, WhoAmI, WhoAmI, WhoAmI, WhoAmI);
    }
}
```

repeat์ด false๋ฉด else๋ก ๋น ์ง๋๋ฐ null๊ฐ์ ๋ฐํํจ
null์ธ ์ํ๋ผ๋ฉด `() => { return $"ThreadName is {Thread.CurrentThread.ManagedThreadId}"; }`์ด ์คํ๋จ

์ฆ, `() => { return $"ThreadName is {Thread.CurrentThread.ManagedThreadId}"; }`์ด ๊ณ์ ์ฌ์ฉ ๋๋๊ฒ์ด ์๋๋ผ ๊ฐ์ด ์์ ๋๋ง ์ฌ์ฉ๋๊ณ 
๊ฐ์ด ์์ผ๋ฉด ํจ์๋ ๋ฐ๋ ์ํจ
