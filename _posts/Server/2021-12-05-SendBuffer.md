---
layout: single
title: "SendBuffer"
categories: Server
tags: Server
author_profile: false
sidebar:
  nav: "docs"
---

## 🙇‍♀️SendBuffer



### 🪐분석


SendBuffer를 외부에서 만드는 이유 : 성능적인 문제 때문, 외부에서 한번만 처리할 문제를 내부에서 만들면 엄청 많이 처리하게 됨

보낼 버퍼의 크기가 가변적일 수 있기 때문에 매우 큰 버퍼를 만들어서 쪼개어 사용하는 방식을 사용함


* SendBufferHelper


`ThreadLocal <SendBuffer> CurrentBuffer`- 전역변수로 만들지만 자기 쓰레드에서만 전역인 변수로 만듦, SendBuffer를 매우 크게 만들어서 쪼개어 사용하므로 재사용을 많이 하니까 전역으로 만들고 싶은거임 근데 멀티쓰레드 환경이므로 경합 문제를 막기 위하여 ThreadLocal 사용, () => { return null; }은 맨 처음 어떤 상태인지 적어 주는 것

ChunkSize - 만들고 싶은 큰 공간의 크기

* SendBufferHelper의 Open
  * CurrentBuffer.Value == null - 값이 없다는 건 젤 처음이니까 ChunkSize만큼 SendBuffer만들기
  * CurrentBuffer.Value.FreeSize < reserveSize - 예약할 공간보다 남은 공간이 작으므로 싹 밀어주고 새 SendBuffer만들기
  * CurrentBuffer.Value.Open - 그제서야 예약 공간 열어주기

SendBufferHelper의 Close - Sendbuffer의 Close와 동일


* SendBuffer


_buffer - SendBufferHelper와 연동됨
_usedSize - 사용된 크기 커서

FreeSize - 총 공간에서 사용된 크기를 뺀 공간 즉, 남은 공간

Open - 공간 예약하기, _usedSize부터 reserveSize만큼 예약
Close - 실제 사용한 buffer, _usedSize부터 usedSize까지 실제 사용한 배열을 반환, _usedSize에 usedSize를 더해 커서를 

### 🪐전체코드

```cs
namespace ServerCore
{
    public class SendBufferHelper
    {
        public static ThreadLocal<SendBuffer> CurrentBuffer = new ThreadLocal<SendBuffer>(() => { return null; });

        public static int ChunkSize { get; set; } = 4096 * 100;

        public static ArraySegment<byte> Open(int reserveSize)
        {
            if (CurrentBuffer.Value == null)
                CurrentBuffer.Value = new SendBuffer(ChunkSize);

            if (CurrentBuffer.Value.FreeSize < reserveSize)
                CurrentBuffer.Value = new SendBuffer(ChunkSize);

            return CurrentBuffer.Value.Open(reserveSize);
        }

        public static ArraySegment<byte> Close(int usedSize)
        {
            return CurrentBuffer.Value.Close(usedSize);
        }
    }

    public class SendBuffer
    {
        byte[] _buffer;
        int _usedSize = 0;

        public int FreeSize { get { return _buffer.Length - _usedSize; } }

        public SendBuffer(int chunkSize)
        {
            _buffer = new byte[chunkSize];
        }

        public ArraySegment<byte> Open(int reserveSize)
        {
            if (reserveSize > FreeSize)
                return null;

            return new ArraySegment<byte>(_buffer, _usedSize, reserveSize);
        }

        public ArraySegment<byte> Close(int usedSize)
        {
            ArraySegment<byte> segment = new ArraySegment<byte>(_buffer, _usedSize, usedSize);
            _usedSize += usedSize;
            return segment;
        }
    }
}
```
