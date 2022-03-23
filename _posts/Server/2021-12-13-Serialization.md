---
layout: single
title: "C# Rookiss Part4 게임서버 : Serialization"
categories: Server
tags: Server
author_profile: false
sidebar:
  nav: "docs"
---


## 🙇‍♀️Serialization


직렬화

* 메모리 상의 어떤 인스턴스로 존재하는 애를 납작하게 만들어서 버퍼안에 밀어넣는 작업 : Serialization
  * 세이브 파일을 만들 때도 serialization 사용

* 바이트 계열에 있는 애를 꺼내 쓰는 것 : Deserialization


### 🪐차근차근



* Serialization1 - TryWriteBite(Span)


  * 메모리 상의 어떤 인스턴스로 존재하는 애를 납작하게 만들어서 버퍼안에 밀어넣는 작업 : Serialization
    * 세이브 파일을 만들 때도 serialization 사용

  * 바이트 계열에 있는 애를 꺼내 쓰는 것 : Deserialization

  * 클라에서 서버로 정보를 줄 때 : playerId 주기
  * 서버에서 클라로 정보를 줄 때 :  hp, attack 주기

  * Recv할 땐 받은 배열에서 뽑아오기

  * PlayerInfoReq를 만들어서 클라에서 서버로 정보 ( playerId)를 주는 상황일 때 
    * SendBufferHelper.Open으로 공간을 열고 BitConverter로 size packetId playerId를 바이트 배열로 저장 Open으로 열어준 ArraySegment에 복사를 하고 Close 후 Send 작업

  * 위의 Send의 단점
    * size packetId playerId를 바이트 배열로 저장하는 부분이 각각 동적으로 배열을 새로 생성한 후 Copy를 통해 OpenSegment에 넣는게 아쉬움
    * 막바로 OpenSegment에 넣으면 좋을 듯 => BitConverter.TryWriteBites( new Span<byte>(s.Array, s.Offset, s.Count ), packet.size); 를 사용 
    * TryWriteBites는 값을 넣을 도착지와 넣을 값을 인자로 받는데 도착지를  Span이라는 것으로 받는다, bool값을 반환하여 success여부를 판단 할 수 있다, Copy부분과 배열을 만드는 부분은 제거

  * size는 결국 다 계산한 뒤에 알 수 있으므로 success &= BitConverter.TryWriteBites( new Span<byte>(s.Array, s.Offset, s.Count ), packet.size);는 마지막에 넣어 주어야 한다
  
  
  
  
* Serialization2 - Serialize, Deserialize 개선
  
  
  * Open후 TryWriteBytes로 밀어넣어주는 부분을 함수형태로 만들기 -> public abstract ArraySegment<byte> Write(), public abstract void Read(ArraySegment<byte> s)

  * Write에 TryWriteBytes부분을 넣어주기

  * ArraySegment는 operate == 로 오버로딩을 해서 null로 구별가능

  * Read부분은 size id를 읽은 뒤에 사용하는 것 이므로 size id는 주석처리, playerId만 사용

  * 패킷 헤더에 있는 값은 믿을 수 없고 참고만 해야 됨

  * 12바이트가 와야하는데 4바이트만 유효범위라고 집어주어도 뒤에 부분이 데이터가 남아있으므로 12바이트까지의 내용이 출력되고 size는 4라고 뜬다 

    * => Read부분 마지막에 BitConverter.ToInt64(new ReadOnlySpan<byte>(s.Array, s.Offset + count, s.Count - count));를 추가하여 범위를 초과한 녀석들은 Exeption이 뜨게 만든다

    * => 이렇게 한다면 마지막에 size값을 작게 준다하더라도 남은 공간이 부족하기 때문에 e발생
  
  
  
  
  
  
* Serialization3 - 가변적인 데이터
  
  
  * Span의 Slice활용 : 첫 SendBufferHelper.Open의 ArraySegment를 Span으로 다시 만들후 TryWriteByte부분을 Slice로 넣어 가독성 높이기, Slice는 그 자체가 변하는 것이 아니라 결과값으로 Span을 반환한다 -> 실질적으로 s의 변화가 있는 것은 아니다

  * Read부분은 ReadOnlySpan으로 만들기

  * 가변적인 데이터를 어떻게 넘기고 추출할까?

    * 2바이트짜리로 string의 크기(string len [2])가 얼마인지 보내고 해당하는 크기의 데이터를 이어서 보내기

    * name = 'ABCD' 일 때 this.name.Length를 하면 크기는 4이지만 바이트로 변환하면 8바이트로 된다 : UTF-16을 사용하고 있기 때문이다 => ushort nameLen = (ushort)Encoding.Unicode.GetByteCount(this.name);으로 해야된다

    * Read부분은 nameLen을 ToUInt16으로 뽑아오고 string데이터는 Encoding.Unicode.GetString()을 사용함 : 바이트를 string으로 바꿔줌
  
Encoding.Unicode.GetString() => byte를 string으로 변환.
Encoding.Unicode.GetBytes() => string를 byte으로 변환.
  
  
```cs
//string
//ushort nameLen = (ushort)Encoding.Unicode.GetByteCount(this.name);
//success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), nameLen);
//count += sizeof(ushort);
//Array.Copy(Encoding.Unicode.GetBytes(this.name), 0, segment.Array, count, nameLen);
//count += nameLen;

// 위에 코드를 개선한 부분
ushort nameLen = (ushort)Encoding.Unicode.GetBytes(this.name, 0, this.name.Length, segment.Array, segment.Offset + count + sizeof(ushort));
success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), nameLen);
count += sizeof(ushort);
count += nameLen;
```
  
  
  
  
* Serialization4 - List Serialization
  
  
  * 리스트는 어떻게 밀어주고 추출할까?

    * 리스트가 int같은것을 받을 땐 string을 받을 때 처럼 크기를 받고 밀어주고 추출가능
  
    * 하지만 구조체를 받을경우 달라짐
  
      * 구초제의 크기만큼 밀어넣어주고 또 구조체내의 인자들을 순회하면서 각각 밀어넣어줌
    
      * Read부분도 마찬가지로 인자가 몇개들어가 있는지 추출하고 순회하면서 구조체를 만들어 추출후 추가 해줌


  
```cs
public struct SkillInfo
{
    public int id;
    public short level;
    public float duration;

    public bool Write(Span<byte> s, ref ushort count)
    {
        bool success = true;
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), id);
        count += sizeof(int);
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), level);
        count += sizeof(short);
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), duration);
        count += sizeof(float);
        return success;
    }

    public void Read(ReadOnlySpan<byte> s, ref ushort count)
    {
        id = BitConverter.ToInt32(s.Slice(count, s.Length - count));
        count += sizeof(int);
        level = BitConverter.ToInt16(s.Slice(count, s.Length - count));
        count += sizeof(short);
        duration = BitConverter.ToSingle(s.Slice(count, s.Length - count));
        count += sizeof(float);
    }
}

// skill list
skills.Clear();
ushort skillLen = BitConverter.ToUInt16(s.Slice(count, s.Length - count));
count += sizeof(ushort);
for (int i = 0; i < skillLen; i++)
{
    SkillInfo skill = new SkillInfo();
    skill.Read(s, ref count);
    skills.Add(skill);
}

// skill list
success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), (ushort)skills.Count);
count += sizeof(ushort);
foreach (SkillInfo skill in skills)
    success &= skill.Write(s, ref count);
  
```
  
  
  * Serialize를 할 때 보통 사용하는것은 구글에서 지원하는 프로토버프 플랫버프 둘 중 하나를 사용함

    * 프로토버프는 중간에 어떤 인스턴스를 만들고 그 인스턴스를 채워서 변환하는 작업을 함 : 성능은 손해가 있어도 코딩이 편함, 직관적임
  
    * 플랫버프는 막바로 데이터를 바이트 배열에 집어 넣음
  
  
  
  
### 🪐코드
                             
  
ServerSesion
                             
```cs
namespace DummyClient
{
    public abstract class Packet
    {
        public ushort size;
        public ushort packetId;

        public abstract ArraySegment<byte> Write();
        public abstract void Read(ArraySegment<byte> s);
    }

    class PlayerInfoReq : Packet
    {
        public long playerId;
        public string name;

        public struct SkillInfo
        {
            public int id;
            public short level;
            public float duration;

            public bool Write(Span<byte> s, ref ushort count)
            {
                bool success = true;
                success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), id);
                count += sizeof(int);
                success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), level);
                count += sizeof(short);
                success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), duration);
                count += sizeof(float);
                return success;
            }

            public void Read(ReadOnlySpan<byte> s, ref ushort count)
            {
                id = BitConverter.ToInt32(s.Slice(count, s.Length - count));
                count += sizeof(int);
                level = BitConverter.ToInt16(s.Slice(count, s.Length - count));
                count += sizeof(short);
                duration = BitConverter.ToSingle(s.Slice(count, s.Length - count));
                count += sizeof(float);
            }
        }

        public List<SkillInfo> skills = new List<SkillInfo>();

        public PlayerInfoReq()
        {
            this.packetId = (ushort)PacketID.PlayerInfoReq;
        }

        public override void Read(ArraySegment<byte> segment)
        {
            ushort count = 0;

            ReadOnlySpan<byte> s = new ReadOnlySpan<byte>(segment.Array, segment.Offset + count, segment.Count);

            //ushort size = BitConverter.ToUInt16(s.Array, s.Offset);
            count += sizeof(ushort);
            //ushort id = BitConverter.ToUInt16(s.Array, s.Offset + count);
            count += sizeof(ushort);
            this.playerId = BitConverter.ToInt64(s.Slice(count, s.Length - count));
            count += sizeof(long);

            // string
            ushort nameLen = BitConverter.ToUInt16(s.Slice(count, s.Length - count));
            count += sizeof(ushort);
            this.name = Encoding.Unicode.GetString(s.Slice(count, nameLen));
            count += nameLen;

            // skill list
            skills.Clear();
            ushort skillLen = BitConverter.ToUInt16(s.Slice(count, s.Length - count));
            count += sizeof(ushort);
            for (int i = 0; i < skillLen; i++)
            {
                SkillInfo skill = new SkillInfo();
                skill.Read(s, ref count);
                skills.Add(skill);
            }
        }

        public override ArraySegment<byte> Write()
        {
            ArraySegment<byte> segment = SendBufferHelper.Open(4096);

            ushort count = 0;
            bool success = true;

            Span<byte> s = new Span<byte>(segment.Array, segment.Offset + count, segment.Count - count);

            //success &= BitConverter.TryWriteBytes(new Span<byte>(s.Array, s.Offset, s.Count), packet.size);
            count += sizeof(ushort);
            success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), this.packetId); // s가 변하는 것이 아니라 Slice하여 Span을 만들어서 넣어줌
            count += sizeof(ushort);
            success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), this.playerId);
            count += sizeof(long);

            //string
            //ushort nameLen = (ushort)Encoding.Unicode.GetByteCount(this.name);
            //success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), nameLen);
            //count += sizeof(ushort);
            //Array.Copy(Encoding.Unicode.GetBytes(this.name), 0, segment.Array, count, nameLen);
            //count += nameLen;

            ushort nameLen = (ushort)Encoding.Unicode.GetBytes(this.name, 0, this.name.Length, segment.Array, segment.Offset + count + sizeof(ushort));
            success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), nameLen);
            count += sizeof(ushort);
            count += nameLen;

            // skill list
            success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), (ushort)skills.Count);
            count += sizeof(ushort);
            foreach (SkillInfo skill in skills)
                success &= skill.Write(s, ref count);

            success &= BitConverter.TryWriteBytes(s, count);

            if (success == false)
                return null;

            return SendBufferHelper.Close(count);
        }
    }

    public enum PacketID
    {
        PlayerInfoReq = 1,
        PlayerInfoOk = 2,
    }

    class ServerSession : Session
    {
        public override void OnConnected(EndPoint endPoint)
        {
            Console.WriteLine($"OnConnected : {endPoint}");

            PlayerInfoReq packet = new PlayerInfoReq() { playerId = 1001 , name = "ABCD"};
            packet.skills.Add(new PlayerInfoReq.SkillInfo() { id = 101, level = 1, duration = 3.0f });
            packet.skills.Add(new PlayerInfoReq.SkillInfo() { id = 201, level = 2, duration = 4.0f });
            packet.skills.Add(new PlayerInfoReq.SkillInfo() { id = 301, level = 3, duration = 5.0f });
            packet.skills.Add(new PlayerInfoReq.SkillInfo() { id = 401, level = 4, duration = 6.0f });

            // 보낸다
            //for (int i = 0; i < 5; i++)
            {
                ArraySegment<byte> s = packet.Write();
                if (s != null)
                    Send(s);
            }
        }

        public override void OnDisconnected(EndPoint endPoint)
        {
            Console.WriteLine($"OnDisconnected : {endPoint}");
        }

        public override int OnRecv(ArraySegment<byte> buffer)
        {
            string recvData = Encoding.UTF8.GetString(buffer.Array, buffer.Offset, buffer.Count);
            Console.WriteLine($"[From Server] {recvData}");

            return buffer.Count;
        }

        public override void OnSend(int numOfBytes)
        {
            Console.WriteLine($"Transferred bytes : {numOfBytes}");
        }
    }
}
```

  
ClientSession
  
```cs
namespace Server
{
    public abstract class Packet
    {
        public ushort size;
        public ushort packetId;

        public abstract ArraySegment<byte> Write();
        public abstract void Read(ArraySegment<byte> s);
    }

    class PlayerInfoReq : Packet
    {
        public long playerId;
        public string name;

        public struct SkillInfo
        {
            public int id;
            public short level;
            public float duration;

            public bool Write(Span<byte> s, ref ushort count)
            {
                bool success = true;
                success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), id);
                count += sizeof(int);
                success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), level);
                count += sizeof(short);
                success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), duration);
                count += sizeof(float);
                return success;
            }

            public void Read(ReadOnlySpan<byte> s, ref ushort count)
            {
                id = BitConverter.ToInt32(s.Slice(count, s.Length - count));
                count += sizeof(int);
                level = BitConverter.ToInt16(s.Slice(count, s.Length - count));
                count += sizeof(short);
                duration = BitConverter.ToSingle(s.Slice(count, s.Length - count));
                count += sizeof(float);
            }
        }

        public List<SkillInfo> skills = new List<SkillInfo>();

        public PlayerInfoReq()
        {
            this.packetId = (ushort)PacketID.PlayerInfoReq;
        }

        public override void Read(ArraySegment<byte> segment)
        {
            ushort count = 0;

            ReadOnlySpan<byte> s = new ReadOnlySpan<byte>(segment.Array, segment.Offset + count, segment.Count);

            //ushort size = BitConverter.ToUInt16(s.Array, s.Offset);
            count += sizeof(ushort);
            //ushort id = BitConverter.ToUInt16(s.Array, s.Offset + count);
            count += sizeof(ushort);
            this.playerId = BitConverter.ToInt64(s.Slice(count, s.Length - count));
            count += sizeof(long);

            // string
            ushort nameLen = BitConverter.ToUInt16(s.Slice(count, s.Length - count));
            count += sizeof(ushort);
            this.name = Encoding.Unicode.GetString(s.Slice(count, nameLen));
            count += nameLen;

            // skill list
            skills.Clear();
            ushort skillLen = BitConverter.ToUInt16(s.Slice(count, s.Length - count));
            count += sizeof(ushort);
            for (int i = 0; i < skillLen; i++)
            {
                SkillInfo skill = new SkillInfo();
                skill.Read(s, ref count);
                skills.Add(skill);
            }
        }

        public override ArraySegment<byte> Write()
        {
            ArraySegment<byte> segment = SendBufferHelper.Open(4096);

            ushort count = 0;
            bool success = true;

            Span<byte> s = new Span<byte>(segment.Array, segment.Offset + count, segment.Count - count);

            //success &= BitConverter.TryWriteBytes(new Span<byte>(s.Array, s.Offset, s.Count), packet.size);
            count += sizeof(ushort);
            success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), this.packetId); // s가 변하는 것이 아니라 Slice하여 Span을 만들어서 넣어줌
            count += sizeof(ushort);
            success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), this.playerId);
            count += sizeof(long);

            //string
            //ushort nameLen = (ushort)Encoding.Unicode.GetByteCount(this.name);
            //success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), nameLen);
            //count += sizeof(ushort);
            //Array.Copy(Encoding.Unicode.GetBytes(this.name), 0, segment.Array, count, nameLen);
            //count += nameLen;

            ushort nameLen = (ushort)Encoding.Unicode.GetBytes(this.name, 0, this.name.Length, segment.Array, segment.Offset + count + sizeof(ushort));
            success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), nameLen);
            count += sizeof(ushort);
            count += nameLen;

            // skill list
            success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), (ushort)skills.Count);
            count += sizeof(ushort);
            foreach (SkillInfo skill in skills)
                success &= skill.Write(s, ref count);

            success &= BitConverter.TryWriteBytes(s, count);

            if (success == false)
                return null;

            return SendBufferHelper.Close(count);
        }
    }

    public enum PacketID
    {
        PlayerInfoReq = 1,
        PlayerInfoOk = 2,
    }

    class ClientSession : PacketSession
    {
        public override void OnConnected(EndPoint endPoint)
        {
            Console.WriteLine($"OnConnected : {endPoint}");

            //Packet packet = new Packet() { size = 100, packetId = 10 };

            //ArraySegment<byte> openSegment = SendBufferHelper.Open(4096);
            //byte[] buffer = BitConverter.GetBytes(packet.size);
            //byte[] buffer2 = BitConverter.GetBytes(packet.packetId);
            //Array.Copy(buffer, 0, openSegment.Array, openSegment.Offset, buffer.Length);
            //Array.Copy(buffer2, 0, openSegment.Array, openSegment.Offset + buffer.Length, buffer2.Length);
            //ArraySegment<byte> sendBuff = SendBufferHelper.Close(buffer.Length + buffer2.Length);

            // 100명
            // 1 -> 이동패킷이 100명
            // 100 -> 이동패킷이 100 * 100 = 1만
            //Send(sendBuff);
            Thread.Sleep(5000);
            Disconnect();
        }

        public override void OnRecvPacket(ArraySegment<byte> buffer)
        {
            ushort count = 0;

            ushort size = BitConverter.ToUInt16(buffer.Array, buffer.Offset);
            count += 2;
            ushort id = BitConverter.ToUInt16(buffer.Array, buffer.Offset + count);
            count += 2;

            switch ((PacketID)id)
            {
                case PacketID.PlayerInfoReq:
                    {
                        PlayerInfoReq p = new PlayerInfoReq();
                        p.Read(buffer);
                        Console.WriteLine($"PlayerInfoReq {p.playerId} {p.name}");

                        foreach (PlayerInfoReq.SkillInfo skill in p.skills)
                        {
                            Console.WriteLine($"skill ({skill.id}) ({skill.level}) ({skill.duration})");
                        }
                    }
                    break;
            }

            Console.WriteLine($"RecvPacketId {id}, Size {size}");
        }

        public override void OnDisconnected(EndPoint endPoint)
        {
            Console.WriteLine($"OnDisconnected : {endPoint}");
        }

        public override void OnSend(int numOfBytes)
        {
            Console.WriteLine($"Transferred bytes : {numOfBytes}");
        }
    }
} 
```
  
  
