---
layout: single
title: "RecvBuffer"
categories: Server
tags: Server
author_profile: false
sidebar:
  nav: "docs"
---


## 🙇‍♀️RecvBuffer



### 🪐분석


TCP정책에선 100바이트를 보내면 무조건 100바이트를 받는다고 할 수 없다. 80바이트만 올수있는데 이런 경우 나머지 20바이트를 추가로 받고 완성된 패킷을 보내야 한다.

RecvBuffer에서는 이러한 부분을 다룰 것이다.

_readPos - 읽기 커서 위치
_writePos - 쓰기 커서 위치

Recv생성자 - BufferSize를 정해 Buffer를 만들어줌

DataSize - 사용한 데이터
FreeSize - 남은 데이터 공간

ReadSegment - 읽기 커서부터 DataSize만큼의 ArraySegment를 반환
WriteSegment - 쓰기 커서부터 남은 공감까지의 ArraySegment를 반환

Clean - 우리가 만든 버퍼를 다쓸 때 초기화 시켜주는 함수, 딱 알맞게 쓰면 읽기 커서와 쓰기 커서를 0으로 이동, 그렇지 않고 찌끄레기가 있는 경우 DataSize만큼 복사하여 첫 위치로 이동 시킨 후 읽기 커서는 0 쓰기 커서는 DataSize만큼 이동

OnRead - 읽기 커서 이동
OnWrite - 쓰기 커서 이동 


### 🪐전체코드

```cs
namespace ServerCore
{
    public class RecvBuffer
    {
        // [r][w][ ][ ][ ][ ][ ][ ]
        ArraySegment<byte> _buffer;
        int _readPos;
        int _writePos;

        public RecvBuffer(int bufferSize)
        {
            _buffer = new ArraySegment<byte>(new byte[bufferSize], 0, bufferSize);
        }

        public int DataSize { get { return _writePos - _readPos; } }
        public int FreeSize { get { return _buffer.Count - _writePos; } }

        public ArraySegment<byte> ReadSegment
        {
            get { return new ArraySegment<byte>(_buffer.Array, _buffer.Offset + _readPos, DataSize); }
        }

        public ArraySegment<byte> WriteSegment
        {
            get { return new ArraySegment<byte>(_buffer.Array, _buffer.Offset + _writePos, FreeSize); }
        }

        public void Clean()
        {
            int dataSize = DataSize;
            if (dataSize == 0)
            {
                // 남은 데이터가 없으면 시작 위치로 커서만 이동
                _readPos = _writePos = 0;
            }
            else
            {
                // 남은 찌끄레기가 있으면 시작 위치로 복사
                Array.Copy(_buffer.Array, _buffer.Offset + _readPos, _buffer.Array, _buffer.Offset, DataSize);
                _readPos = 0;
                _writePos = dataSize;
            }
        }

        public bool OnRead(int numOfBytes)
        {
            if (numOfBytes > DataSize)
                return false;

            _readPos += numOfBytes;
            return true;
        }

        public bool OnWrite(int numOfBytes)
        {
            if (numOfBytes > FreeSize)
                return false;

            _writePos += numOfBytes;
            return true;
        }
    }
}
```
