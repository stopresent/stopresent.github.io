---
layout: single
title: "C Sharp Rookiss Part1 C Sharp 기초 프로그래밍 입문 : List, Dictionary"
categories: CS
tags: CS
author_profile: false
sidebar:
  nav: "docs"
---

## 🙇‍♀️List, Dictionary

### List

* List는 동적배열
  * 공간이 부족하면 공간이 큰 배열을 만든다
  * 그 배열에 원본을 넣는다
  * 원본과 배열을 바꾼다
* List는 Class이므로 참조형태이다

`List<int> list = new List<int>();` - 선언 방식

`using System.Collections.Generic;` List를 사용하려면 적어야 됨!

* List.Add

```cs
for (int i = 0; i < list.Count; i++)
{
    list.Add(i);
}

foreach (int num in list)
{
    list.Add(num);
}
```
* list.Count로 list의 개수를 파악
  * 배열은 Length!


* 삽입과 삭제

`list.Insert(2, 999);`
> 인데스번호와 삽입할 숫자를 넣는다

`bool isRemove = list.Remove(3);`
> 제거할 값을 입력한다
  > 첫번째로 만난 값을 삭제한다 (뒤에 같은 값을 만나도 삭제 안함)
  > bool형태로 값을 반환한다 (삭제 O X)

`list.RemoveAt(2);`
> 인덱스 번호에 있는 값을 삭제한다

`list.Clear();`
> 리스트 전체를 삭제한다

* 리스트는 중간에 삽입혹은 삭제가 효율적이지 않다!

### Dictionary

* 키 와 값을 인자로 받는다
  * 키 값을 알면 빠르게 값을 찾을 수 있다

`Dictionary<int, Monster> dic = new Dictionary<int, Monster>();` - 선언 방식

```cs
class Monster
{
    public int id;

    public Monster(int id)
    {
        this.id = id;
    }
}
```
Monster클래스를 만들어 생성자로 id를 받는다

`dic.Add(1, new Monster(1));`

`dic[5] = new Monster(5);`

-> 1의 id를 가진 몬스터와 5의 id를 가진 몬스터가 생성됨
-> 0번 인덱스엔 1, 1번 인덱스엔 5가 채워짐

```cs
for (int i = 0; i < 10000; i++)
{
    dic.Add(i, new Monster(i));
}
```
10000번까지의 id를 가진 몬스터를 만든후 5000번의 id를 가진 몬스터 추출하기

`Monster mon = dic[5000];` - 추출 방법
* 5000번이 없었다면 크래쉬가 남

`Monster mon2;`

`bool found = dic.TryGetValue(20000, out mon2);`

* 이렇게 값이 있는지 체크 후 추출하는 것이 안전함
  * TryGetValue는 bool값을 반환
  * 첫 인자는 키를 적고 두번째 값은 out를 적음
  * 위의 경우 20000의 id가 없으므로 mon2는 null을 반환

* 삭제

`dic.Remove(100);`
> 100의 키를 가진 값을 삭제

`dic.Clear();`
> dic의 전체 삭제
