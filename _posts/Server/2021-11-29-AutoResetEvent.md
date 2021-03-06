---
layout: single
title: "C# Rookiss Part4 ๊ฒ์์๋ฒ : AutoResetEvent"
categories: Server
tags: Server
author_profile: false
sidebar:
  nav: "docs"
---

## ๐โโ๏ธEvent

![๊ฐ์ง ๋ฉํ](https://user-images.githubusercontent.com/86364202/143811611-8673eeff-b3b5-4acd-844f-d8cec3c9df2f.png)

์ฌ๊ธฐ์ ์ง์์ ์ปค๋๋ ๋ฒจ์ ์๋ ์ฆ, ์๋ ๊ด๋ฆฌ์์ ์๋ ์ง์์ผ๋ก ์ด๋ง์ด๋งํ๊ฒ ๋๋ฆผ

ํ์ง๋ง ์ฐ๋ ๋ ์์ฅ์์  ์๊ฐ์ ๋ญ๋นํ์ง ์๊ณ  100ํผ ์๋ฌผ์ ๊ฐ ํ๋ฆด๋ ํ์ฅ์ค์ ๋ค์ด๊ฐ

### ๐ชAutoResetEvent

*ํจ๊ฒ์ดํธ!
  * ๋ฌธ์ด ์๋์ผ๋ก ์ ๊ธด๋ค
  * ์ฐจ๊ฐ ํ๋์ฉ ์ง๋๊ฐ๋ค


```cs
class Lock
{
    // bool <= ์ปค๋
    AutoResetEvent _available = new AutoResetEvent(true); // ํจ๊ฒ์ดํธ ์ฒซ๋ฌธ์ ์ฐ ์ํ(true)

    public void Acquire()
    {
        _available.WaitOne(); // ์์ฅ ์๋, ์๋์ ๋ฌธ๋ ๋ซ์์ค!
        //_available.Reset(); // bool = false // WaitOne์ ํฌํจ๋์ด์๋ค
    }

    public void Release()
    {
        _available.Set(); // flag = true
    }
}
```

`AutoResetEvent _available = new AutoResetEvent(true);`์ ์ปค๋๋จ์ bool๊ณผ ๊ฐ๋ค๊ณ  ์๊ฐํ๋ฉด ๋จ

`_available.WaitOne();` ์ `_available.Reset();`์ด ํฌํจ๋์ด ์๋ค 
`_available.Reset();`์ bool = false ๋ผ๋ ๋ป


### ๐ชManualResetEvent

๋ฌธ์ด ์๋์ผ๋ก ์ ๊ธด๋ค

๋ฌธ์ ๋ซ์ง์์ผ๋ฉด ๋ช๋์ ์ฐจ๋  ์ง์์ด๋  ์ง๋๊ฐ๋ค


```cs
class Lock
{
    // bool <= ์ปค๋
    ManualResetEvent _available = new ManualResetEvent(true);

    public void Acquire()
    {
        _available.WaitOne(); // ์์ฅ ์๋
        _available.Reset(); // ๋ฌธ์ ๋ซ๋๋ค
    }

    public void Release()
    {
        _available.Set(); // ๋ฌธ์ ์ด์ด์ค๋ค
    }
}
```

์ด๋ ๊ฒ ์ฝ๋๋ฅผ ์ง๋ฉด ๋ฌธ์์ด๊ณ  ๋ซ๋ ๋ถ๋ถ์ด ์์์ ์ผ๋ก ์ฒ๋ฆฌ๊ฐ ์๋จ

* ๋งค๋ด์ผ๋ฆฌ์์ด๋ฒคํธ๊ฐ ํ์ํ ๋ถ๋ถ
  * ๋ชจ๋  ๋ก๋ฉ์ด ๋๋๊ณ  ์ฐ๋ ๋๋ฅผ ํ์ด์ฃผ๋ ๊ฒฝ์ฐ
    * `ManualResetEvent(false);`๋ก ๋ฌธ์ ๋ซ์์ค
    * ๋ก๋ฉ์ด ๋๋๋ฉด _available.WaitOne(); // ์์ฅ ์๋๋ฅผ ํ ์ ๋ค์ด
    * _available.Set(); // ๋ฌธ์ ์ด์ด์ฃผ๊ณ  ์ฐ๋ ๋๋ค์ด ์ผ์ ์์


### ๐ชMutex

```cs
namespace ServerCore
{
    class Program
    {
        static int _num = 0;
        static Mutex _lock = new Mutex();

        static void Thread_1()
        {
            for (int i = 0; i < 100000; i++)
            {
                _lock.WaitOne();
                _num++;
                _lock.ReleaseMutex();
            }
        }

        static void Thread_2()
        {
            for (int i = 0; i < 100000; i++)
            {
                _lock.WaitOne();
                _num--;
                _lock.ReleaseMutex();
            }
        }

        static void Main(string[] args)
        {
            Task t1 = new Task(Thread_1);
            Task t2 = new Task(Thread_2);

            t1.Start();
            t2.Start();

            Task.WaitAll(t1, t2);

            Console.WriteLine(_num);
        }
    }
}
```

์ปค๋ ๋๊ธฐํ ๊ฐ์ฒด์ฌ์ ๋งค์ฐ ๋๋ฆผ

Mutex๋ ์ด๋ฒคํธ๋ณด๋ค ๋ ๋ง์ ์ ๋ณด๋ฅผ ๊ฐ์ง!

* ๋ช๋ฒ ์ ๊ถ๋์ง ์นด์ดํ
* ThreadId๋ฅผ ๊ฐ์ง๊ณ  ์์ด ์๋ฑํ ์ฐ๋ ๋๋ฅผ ๋ฆด๋ฆฌ์ฆ ํ์ง ์๊ฒ ํจ


**AutoResetEvent, ManualResetEvent, Mutext ๋ชจ๋ ์ปค๋๋จ์์ ๋์!**

๊ทธ๋์ ์ด๋ง์ด๋งํ๊ฒ ๋๋ฆฌ๋ค!!






















