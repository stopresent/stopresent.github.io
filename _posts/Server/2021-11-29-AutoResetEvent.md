---
layout: single
title: "C# Rookiss Part4 게임서버 : AutoResetEvent"
categories: Server
tags: Server
author_profile: false
sidebar:
  nav: "docs"
---

## 🙇‍♀️Event

![갑질 메타](https://user-images.githubusercontent.com/86364202/143811611-8673eeff-b3b5-4acd-844f-d8cec3c9df2f.png)

여기서 직원은 커널레벨에 있는 즉, 아래 관리자에 있는 직원으로 어마어마하게 느림

하지만 쓰레드 입장에선 시간을 낭비하지 않고 100퍼 자물쇠가 풀릴때 화장실을 들어감

### 🪐AutoResetEvent

*톨게이트!
  * 문이 자동으로 잠긴다
  * 차가 한대씩 지나간다


```cs
class Lock
{
    // bool <= 커널
    AutoResetEvent _available = new AutoResetEvent(true); // 톨게이트 첫문은 연 상태(true)

    public void Acquire()
    {
        _available.WaitOne(); // 입장 시도, 자동을 문도 닫아줌!
        //_available.Reset(); // bool = false // WaitOne에 포함되어있다
    }

    public void Release()
    {
        _available.Set(); // flag = true
    }
}
```

`AutoResetEvent _available = new AutoResetEvent(true);`은 커널단의 bool과 같다고 생각하면 됨

`_available.WaitOne();` 에 `_available.Reset();`이 포함되어 있다 
`_available.Reset();`은 bool = false 라는 뜻


### 🪐ManualResetEvent

문이 수동으로 잠긴다

문을 닫지않으면 몇대의 차든 직원이든 지나간다


```cs
class Lock
{
    // bool <= 커널
    ManualResetEvent _available = new ManualResetEvent(true);

    public void Acquire()
    {
        _available.WaitOne(); // 입장 시도
        _available.Reset(); // 문을 닫는다
    }

    public void Release()
    {
        _available.Set(); // 문을 열어준다
    }
}
```

이렇게 코드를 짜면 문을열고 닫는 부분이 원자적으로 처리가 안됨

* 매뉴얼리셋이벤트가 필요한 부분
  * 모든 로딩이 끝나고 쓰레드를 풀어주는 경우
    * `ManualResetEvent(false);`로 문을 닫아줌
    * 로딩이 끝나면 _available.WaitOne(); // 입장 시도를 한 애들이
    * _available.Set(); // 문을 열어주고 쓰레드들이 일을 시작


### 🪐Mutex

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

커널 동기화 개체여서 매우 느림

Mutex는 이벤트보다 더 많은 정보를 가짐!

* 몇번 잠궜는지 카운팅
* ThreadId를 가지고 있어 엉뚱한 쓰레드를 릴리즈 하지 않게 함


**AutoResetEvent, ManualResetEvent, Mutext 모두 커널단에서 동작!**

그래서 어마어마하게 느리다!!






















