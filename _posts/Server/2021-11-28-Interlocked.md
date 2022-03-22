---
layout: single
title: ""
categories: Server
tags: Server
author_profile: false
sidebar:
  nav: "docs"
---

## 🙇‍♀️Interlocked


### 🪐경합 조건 (Race Condition)

2번 테이블에서 콜라 하나를 주문하고 주문현황에 올라옴
직원 3명이 동시에 주문현황을 보고 콜라르 2번 테이블에 줌
결국 2번 테이블에 콜라를 3개 받음

순서를 지키지 않고 동시다발적으로 일을 해서 생긴 문제

* 경합 조건 예시

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

100000을 더하는 쓰레드와 100000을 빼는 쓰레드
- 0이 아니라 다른 결과 값이 나옴
- 이유는 number++, number--가 원자적으로 처리 되는것이 아니기 때문

```cs
number++; // 어셈블리언어로 밑과 같이 처리 됨

int temp = number;
temp += 1;
number = temp;
```

### 🪐Interlocked

* 원자적으로 처리해주는 함수
- Interlocked 계열의 함수
- 가시성 문제까지 해결함 - 메모리베리어가 숨어있음
- volatile이 필요없음

```cs
static int number = 0;

static void Thread_1()
{
    // atomic = 원자성
    
    for (int i = 0; i < 100000; i++)
        // All or Nothing
        Interlocked.Increment(ref number); // ref로 참조를 해서 직접 값을 올린다
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

interlocked계열 함수로 해결가능


* 멀티쓰레드의 함정

```cs
int pre = number;
Interlocked.Increment(ref number);
int next = number;
```

언뜻보면 next는 pre보다 1클것 같지만 알수없다

싱글 쓰레드에서는 가능하지만 멀티쓰레드에서는 누군가가 보고 값이 바뀔수 있기 때문!!

`int afterValue = Interlocked.Increment(ref number);`값을 빼올려면 이렇게 해야됨







