---
layout: single
title: "ReaderWriterLock"
categories: Server
tags: Server
author_profile: false
sidebar:
  nav: "docs"
---

## 🙇‍♀️ReaderWriterLock

* 읽을때(get)는 락이 없는 것처럼 사용할수 있지만 쓸때는 락을 걸어서 사용하는 것
* ReaderWriterLockSlim이 최신버전!!
* 대부분 락이 필요없지만 적은 확률로 락이 필요할때 사용한다


### 🪐Lock, SpinLock

* 락을 구현하는 방법
  * 근성(스핀락)
  * 양보(yield)
  * 갑질(이벤트 방식)

**상호배제적**

```cs
static Object _lock = new object();
static SpinLock _lock2 = new SpinLock(); // 너무 오래걸리면 양보함

static void Main(string[] args)
{
    lock (_lock)
    {

    }

    bool lockTacken = false; // 스핀락은 불값의 키가 필요함
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

락은 내부적으로 Monitor로 구현됨

일반적인경우 그냥 lock()을 사용하면 될거같음 - 편해보임

스핀락은 bool키가 필요하고 try, finally로 들어가고 나가야 됨


### 🪐ReaderWriterLock예시


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

읽을 땐 편하게 읽지만 쓸땐 차례를 지키시오.







