---
layout: single
title: "C# Rookiss Part4 게임서버 : ReaderWriterLock구현연습"
categories: Server
tags: Server
author_profile: false
sidebar:
  nav: "docs"
---

## 🙇‍♀️ReaderWriterLock구현 연습


### 🪐재귀적 락을 허용안함

```cs
namespace ServerCore
{
    // 재귀적 락을 허용할지 (No)
    // 스핀락 정책 (5000 -> Yield)
    class Lock
    {
        const int EMPTY_FLAG = 0x00000000;
        const int WRITE_MASK = 0x7FFF0000;
        const int READ_MASK = 0x0000FFFF;
        const int MAX_SPINT_COUNT = 5000;

        // [Unused(1)] [WriteThreadId(15)] [ReadCount(16)]
        int _flag = EMPTY_FLAG;

        public void WriteLock()
        {
            // 아무도 WriteLock or ReadLock을 획득하고 있지 않을 때, 경합해서 소유권을 얻는다
            int desired = (Thread.CurrentThread.ManagedThreadId << 16) & WRITE_MASK;
            while (true)
            {
                for (int i = 0; i < MAX_SPINT_COUNT; i++)
                {
                    // 시도를 해서 성공하면 return
                    if (Interlocked.CompareExchange(ref _flag, desired, EMPTY_FLAG) == EMPTY_FLAG)
                        return;
                    //if (_flag == EMPTY_FLAG)
                    //    _flag = desired;
                }
                Thread.Yield();
            }
        }

        public void WriteUnlock()
        {
            Interlocked.Exchange(ref _flag, EMPTY_FLAG);
        }

        public void ReadLock()
        {
            // 아무도 WriteLock을 가지고 있지 않으면, ReadCount를 1 늘린다
            while (true)
            {
                for (int i = 0; i < MAX_SPINT_COUNT; i++)
                {
                    int expected = (_flag & READ_MASK);
                    if (Interlocked.CompareExchange(ref _flag, expected + 1, expected) == expected) // 생각해보기
                        return;
                    //if ((_flag & WRITE_MASK) == 0)
                    //    _flag += 1;
                }
                Thread.Yield();
            }
        }

        public void ReadUnlock()
        {
            Interlocked.Decrement(ref _flag);
        }
    }
}
```

### 🪐재귀적 락을 허용함

```cs
namespace ServerCore
{
    // 재귀적 락을 허용할지 (Yes) WriteLock -> WriteLock Ok, WriteLock -> ReadLock Ok, ReadLock -> WriteLock No
    // 스핀락 정책 (5000 -> Yield)
    class Lock
    {
        const int EMPTY_FLAG = 0x00000000;
        const int WRITE_MASK = 0x7FFF0000;
        const int READ_MASK = 0x0000FFFF;
        const int MAX_SPINT_COUNT = 5000;

        // [Unused(1)] [WriteThreadId(15)] [ReadCount(16)]
        int _flag = EMPTY_FLAG;
        int _writeCount = 0;

        public void WriteLock()
        {
            // 동일 쓰레드가 WriteLock을 이미 획들하고 있는지 확인
            int lockThreadId = (_flag & WRITE_MASK) >> 16;
            if (Thread.CurrentThread.ManagedThreadId == lockThreadId)
            {
                _writeCount++;
                return;
            }

            // 아무도 WriteLock or ReadLock을 획득하고 있지 않을 때, 경합해서 소유권을 얻는다
            int desired = (Thread.CurrentThread.ManagedThreadId << 16) & WRITE_MASK;
            while (true)
            {
                for (int i = 0; i < MAX_SPINT_COUNT; i++)
                {
                    // 시도를 해서 성공하면 return
                    if (Interlocked.CompareExchange(ref _flag, desired, EMPTY_FLAG) == EMPTY_FLAG)
                    {
                        _writeCount = 1;
                        return;
                    }
                    //if (_flag == EMPTY_FLAG)
                    //    _flag = desired;
                }
                Thread.Yield();
            }
        }

        public void WriteUnlock()
        {
            int lockCount = --_writeCount;
            if (lockCount == 0)
                Interlocked.Exchange(ref _flag, EMPTY_FLAG);
        }

        public void ReadLock()
        {
            // 동일 쓰레드가 WriteLock을 이미 획들하고 있는지 확인
            int lockThreadId = (_flag & WRITE_MASK) >> 16;
            if (Thread.CurrentThread.ManagedThreadId == lockThreadId)
            {
                Interlocked.Increment(ref _flag);
                return;
            }


            // 아무도 WriteLock을 가지고 있지 않으면, ReadCount를 1 늘린다
            while (true)
            {
                for (int i = 0; i < MAX_SPINT_COUNT; i++)
                {
                    int expected = (_flag & READ_MASK);
                    if (Interlocked.CompareExchange(ref _flag, expected + 1, expected) == expected)
                        return;
                    //if ((_flag & WRITE_MASK) == 0)
                    //    _flag += 1;
                }
                Thread.Yield();
            }
        }

        public void ReadUnlock()
        {
            Interlocked.Decrement(ref _flag);
        }
    }
}
```

* ReadLock에서 `if (Interlocked.CompareExchange(ref _flag, expected + 1, expected) == expected)`부분의 의미
  * `int expected = (_flag & READ_MASK);` - expected의 뜻
  * READ_MASK가 이미 WriteThreadId부분이 0이므로 1차 체크
  * 동시에 ReadCount를 1 올릴수있음 - 원자적 처리


* WritLock, WriteUnlock, ReadLock, ReadUnlock의 구조
* ReadLock은 상호배제적이지 않기 때문에 하나하나만 들어가는 것이 아니다
* WriteLock은 상호배제적으로 하나씩만 들어간다 (화장실 개념)

