---
layout: single
title: "C# Rookiss Part4 ๊ฒ์์๋ฒ : ReaderWriterLock"
categories: Server
tags: Server
author_profile: false
sidebar:
  nav: "docs"
---

## ๐โโ๏ธReaderWriterLock

* ์ฝ์๋(get)๋ ๋ฝ์ด ์๋ ๊ฒ์ฒ๋ผ ์ฌ์ฉํ ์ ์์ง๋ง ์ธ๋๋ ๋ฝ์ ๊ฑธ์ด์ ์ฌ์ฉํ๋ ๊ฒ
* ReaderWriterLockSlim์ด ์ต์ ๋ฒ์ !!
* ๋๋ถ๋ถ ๋ฝ์ด ํ์์์ง๋ง ์ ์ ํ๋ฅ ๋ก ๋ฝ์ด ํ์ํ ๋ ์ฌ์ฉํ๋ค


### ๐ชLock, SpinLock

* ๋ฝ์ ๊ตฌํํ๋ ๋ฐฉ๋ฒ
  * ๊ทผ์ฑ(์คํ๋ฝ)
  * ์๋ณด(yield)
  * ๊ฐ์ง(์ด๋ฒคํธ ๋ฐฉ์)

**์ํธ๋ฐฐ์ ์ **

```cs
static Object _lock = new object();
static SpinLock _lock2 = new SpinLock(); // ๋๋ฌด ์ค๋๊ฑธ๋ฆฌ๋ฉด ์๋ณดํจ

static void Main(string[] args)
{
    lock (_lock)
    {

    }

    bool lockTacken = false; // ์คํ๋ฝ์ ๋ถ๊ฐ์ ํค๊ฐ ํ์ํจ
    try
    {
        _lock2.Enter(ref lockTacken);
    }
    finally
    {
        if (lockTacken)
            _lock2.Exit();
    }
}
```

๋ฝ์ ๋ด๋ถ์ ์ผ๋ก Monitor๋ก ๊ตฌํ๋จ

์ผ๋ฐ์ ์ธ๊ฒฝ์ฐ ๊ทธ๋ฅ lock()์ ์ฌ์ฉํ๋ฉด ๋ ๊ฑฐ๊ฐ์ - ํธํด๋ณด์

์คํ๋ฝ์ boolํค๊ฐ ํ์ํ๊ณ  try, finally๋ก ๋ค์ด๊ฐ๊ณ  ๋๊ฐ์ผ ๋จ


### ๐ชReaderWriterLock์์


```cs
static ReaderWriterLockSlim _lock3 = new ReaderWriterLockSlim();

class Reward
{

}

static Reward GetRewardById(int id)
{
    _lock3.EnterReadLock();

    _lock3.ExitReadLock();
    return null;
}

static void AddReward(Reward reward)
{
    _lock3.EnterWriteLock();

    _lock3.ExitWriteLock();
}
```

์ฝ์ ๋ ํธํ๊ฒ ์ฝ์ง๋ง ์ธ๋ ์ฐจ๋ก๋ฅผ ์งํค์์ค.







