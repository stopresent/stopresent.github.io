---
layout: single
title: "C# Rookiss Part4 ๊ฒ์์๋ฒ : Interlocked"
categories: Server
tags: Server
author_profile: false
sidebar:
  nav: "docs"
---

## ๐โโ๏ธInterlocked


### ๐ช๊ฒฝํฉ ์กฐ๊ฑด (Race Condition)

2๋ฒ ํ์ด๋ธ์์ ์ฝ๋ผ ํ๋๋ฅผ ์ฃผ๋ฌธํ๊ณ  ์ฃผ๋ฌธํํฉ์ ์ฌ๋ผ์ด
์ง์ 3๋ช์ด ๋์์ ์ฃผ๋ฌธํํฉ์ ๋ณด๊ณ  ์ฝ๋ผ๋ฅด 2๋ฒ ํ์ด๋ธ์ ์ค
๊ฒฐ๊ตญ 2๋ฒ ํ์ด๋ธ์ ์ฝ๋ผ๋ฅผ 3๊ฐ ๋ฐ์

์์๋ฅผ ์งํค์ง ์๊ณ  ๋์๋ค๋ฐ์ ์ผ๋ก ์ผ์ ํด์ ์๊ธด ๋ฌธ์ 

* ๊ฒฝํฉ ์กฐ๊ฑด ์์

```cs
static int number = 0;

static void Thread_1()
{
    for (int i = 0; i < 100000; i++)
        number++;
}

static void Thread_2()
{
    for (int i = 0; i < 100000; i++)
        number--;
}

static void Main(string[] args)
{
    Task t1 = new Task(Thread_1);
    Task t2 = new Task(Thread_2);
    t1.Start();
    t2.Start();

    Task.WaitAll(t1, t2);

    Console.WriteLine(number);
}
```

100000์ ๋ํ๋ ์ฐ๋ ๋์ 100000์ ๋นผ๋ ์ฐ๋ ๋
- 0์ด ์๋๋ผ ๋ค๋ฅธ ๊ฒฐ๊ณผ ๊ฐ์ด ๋์ด
- ์ด์ ๋ number++, number--๊ฐ ์์์ ์ผ๋ก ์ฒ๋ฆฌ ๋๋๊ฒ์ด ์๋๊ธฐ ๋๋ฌธ

```cs
number++; // ์ด์๋ธ๋ฆฌ์ธ์ด๋ก ๋ฐ๊ณผ ๊ฐ์ด ์ฒ๋ฆฌ ๋จ

int temp = number;
temp += 1;
number = temp;
```

### ๐ชInterlocked

* ์์์ ์ผ๋ก ์ฒ๋ฆฌํด์ฃผ๋ ํจ์
- Interlocked ๊ณ์ด์ ํจ์
- ๊ฐ์์ฑ ๋ฌธ์ ๊น์ง ํด๊ฒฐํจ - ๋ฉ๋ชจ๋ฆฌ๋ฒ ๋ฆฌ์ด๊ฐ ์จ์ด์์
- volatile์ด ํ์์์

```cs
static int number = 0;

static void Thread_1()
{
    // atomic = ์์์ฑ
    
    for (int i = 0; i < 100000; i++)
        // All or Nothing
        Interlocked.Increment(ref number); // ref๋ก ์ฐธ์กฐ๋ฅผ ํด์ ์ง์  ๊ฐ์ ์ฌ๋ฆฐ๋ค
}

static void Thread_2()
{
    for (int i = 0; i < 100000; i++)
        Interlocked.Decrement(ref number);
}

static void Main(string[] args)
{
    Task t1 = new Task(Thread_1);
    Task t2 = new Task(Thread_2);
    t1.Start();
    t2.Start();

    Task.WaitAll(t1, t2);

    Console.WriteLine(number);
}
```

interlocked๊ณ์ด ํจ์๋ก ํด๊ฒฐ๊ฐ๋ฅ


* ๋ฉํฐ์ฐ๋ ๋์ ํจ์ 

```cs
int pre = number;
Interlocked.Increment(ref number);
int next = number;
```

์ธ๋ป๋ณด๋ฉด next๋ pre๋ณด๋ค 1ํด๊ฒ ๊ฐ์ง๋ง ์์์๋ค

์ฑ๊ธ ์ฐ๋ ๋์์๋ ๊ฐ๋ฅํ์ง๋ง ๋ฉํฐ์ฐ๋ ๋์์๋ ๋๊ตฐ๊ฐ๊ฐ ๋ณด๊ณ  ๊ฐ์ด ๋ฐ๋์ ์๊ธฐ ๋๋ฌธ!!

`int afterValue = Interlocked.Increment(ref number);`๊ฐ์ ๋นผ์ฌ๋ ค๋ฉด ์ด๋ ๊ฒ ํด์ผ๋จ







