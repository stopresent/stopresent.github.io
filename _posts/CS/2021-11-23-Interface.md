---
layout: single
title: "C Sharp Rookiss Part1 기초 프로그래밍 입문 : Interface"
categories: CS
tags: CS
author_profile: false
sidebar:
  nav: "docs"
---

## 🙇‍♀️Interface


### 🪐추상 클래스

```cs
abstract class Monster
{
    public abstract void Shout();
}
```

* 추상 클래스는 class 앞에 abstract를 붙임
* 정의를 하면 안됨 - {}없음
* 함수는 `public abstract void Shout();` 형식
  * 자식들은 override해서 함수를 무조건 만들어야함
  * 다중 상속이 안됨

```cs
class Orc : Monster
{
    public override void Shout()
    {
        Console.WriteLine("록타르 오가르~!");
    }
}
```

### 🪐Interface

```cs
interface IFlyable
{
    void Fly();
}
```

* interface와 이름만 적음
* 함수는 반환 형식만 적음
* 다중상속이 가능함
* override할 필요가 없지만 무조건 함수를 만들어야함

```cs
class FlyableOrc : Orc, IFlyable
{
    public void Fly() { }
}
```

### 🪐Interface 응용

```cs
static void DoFly(IFlyable flyable)
{
    flyable.Fly();
}

static void Main(string[] args)
{
    FlyableOrc orc = new FlyableOrc();
    DoFly(orc);
}
```

* `IFlyable orc = new FlyableOrc();` - 선언 가능
* 특정 인터페이스를 가진 것으로 함수를 만들기 가능
