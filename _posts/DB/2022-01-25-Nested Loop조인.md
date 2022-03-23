---
layout: single
title: "Rookiss Part6 SQL 웹서버 : Nested Loop조인"
categories: DB
tags: DB
author_profile: false
sidebar:
  nav: "docs"
---


## 🙇‍♀️Nested Loop조인


**조인 원리**
1. Nested Loop (NL) 조인
2. Merge (병합) 조인
3. Hash (해시) 조인


### 🪐각각의 경우에 따른 조인 


**Merge**

```
SELECT *
FROM players AS p
	INNER JOIN salaries AS s
	ON p.playerID = s.playerID;
```


**NL**

```
SELECT TOP 5 *
FROM players AS p
	INNER JOIN salaries AS s
	ON p.playerID = s.playerID;
```


**Hash**

```
SELECT *
FROM salaries AS s
	INNER JOIN teams AS t
	ON s.teamID = t.teamID;
```


**NL**

```
SELECT *
FROM players AS p
	INNER JOIN salaries AS s
	ON p.playerID = s.playerID
	OPTION(LOOP JOIN);
```


**인덱스 순서 강제**

```
SELECT *
FROM players AS p
	INNER JOIN salaries AS s
	ON p.playerID = s.playerID
	OPTION(FORCE ORDER, LOOP JOIN)
```



### 🪐Nested Loop조인이 구조


Nested : 내포하는, 중첩된

중첩된 루프라는 뜻으로 이중for문과 구조가 비슷하다

내부에서 하나하나 찾는 느낌

내부에 있는 집합이 빠르게 찾을 수 있으면 성능이 더 좋아진다

```cs
foreach (Player p in players)
{
	foreach (Salary s in salaries)
	{
		if (s.playerId == p.playerId)
		{
			result.Add(p.playerId);
			break;
		}
	}
}
```

최종 결과물에 count를 둔다면 아무리 많이 루프를 돌아도 나름 빠른 것이 특징


### 🪐결론

**NL 특징**
1. 먼저 액세스한 (OUTER)테이블의 로우를 차례 차레 -> (INNER)테이블에 랜덤 액세스
2. (INNER)테이블에 인덱스가 없다면 노답
3. 부분범위 처리에 좋다 (ex. TOP 5)
















