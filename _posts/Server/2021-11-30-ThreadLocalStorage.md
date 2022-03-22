---
layout: single
title: "ThreadLocalStorage"
categories: Server
tags: Server
author_profile: false
sidebar:
  nav: "docs"
---


## 🙇‍♀️TLS

* 전역변수인데 쓰레드마다 고유하게 접근할수있는 전역변수
- 직원에게 한꺼번에 일시키기 (큰 쟁반에 반찬, 메인 메뉴 등등을 올려서 서빙)
- 스택에 넣고싶지만 스택은 불안정함
- TLS를 만들어서 사용!!


### 🪐TLS

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
        // 전역변수인데 쓰레드마다 고유하게 접근할수있는 전역변수
        Parallel.Invoke(WhoAmI, WhoAmI, WhoAmI, WhoAmI, WhoAmI, WhoAmI);
    }
}
```

Parallel.Invoke() 안에 들어간 Action만큼 테스크를 만들어 실행 시켜줌
쓰레드 풀에서 만들어짐

* 문제점
  * 일꾼이 일하러 갔다오면 값을 덮어써서 다시 일함

### 🪐TLS repeat

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

repeat이 false면 else로 빠지는데 null값을 반환함
null인 상태라면 `() => { return $"ThreadName is {Thread.CurrentThread.ManagedThreadId}"; }`이 실행됨

즉, `() => { return $"ThreadName is {Thread.CurrentThread.ManagedThreadId}"; }`이 계속 사용 되는것이 아니라 값이 없을 때만 사용되고
값이 있으면 함수는 발동 안함
