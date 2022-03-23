---
layout: single
title: "Rookiss Part5 데이터베이스 : INSERT DELETE UPDATE"
categories: DB
tags: DB
author_profile: false
sidebar:
  nav: "docs"
---


## 🙇‍♀️INSERT DELETE UPDATE


### 🪐INSERT

**INSERT**

* 연습할 부분 (salaries)
```
SELECT *
FROM salaries
ORDER BY yearID DESC
```

* INSERT INTO [테이블명] VALUES (값, ...)
```
INSERT INTO salaries
VALUES (2022, 'KOR', 'NL', 'presnet', 99999999)
```


* 데이터를 하나 빼먹으면? -> Error
```
INSERT INTO salaries
VALUES (2022, 'KOR', 'NL', 'present2')
```

* INSERT INTO [테이블명] (열, ...) VALUES (값, ...)
```
INSERT INTO salaries(yearID, teamID, playerID, lgID, salary)
VALUES (2022, 'KOR', 'present2', 'NL', 9999999)
```

* 테이블 설정에 따라 모든 정보를 입력하지 않아도 된다
* salaries는 연봉이 기본 NULL
```
INSERT INTO salaries(yearID, teamID, playerID, lgID)
VALUES (2022, 'KOR', 'present3', 'NL')
```


### 🪐DELETE vs UPDATE

* DELETE FROM [테이블명] WHERE [조건]
* WHERE를 안붙이면 통채로 사라짐
```
DELETE FROM salaries
WHERE playerID = 'present3'
```

* UPDATE [테이블명] SET [열 = 값, ] WHERE [조건]
* WHERE를 안붙이면 모든 조건에서 실행됨
```
UPDATE salaries
SET salary = salary * 2, yearID = yearID + 1
WHERE teamID = 'KOR'
```

```
DELETE FROM salaries
WHERE yearID > 2020
```

* DELETE vs UPDATE
* 물리적삭제 vs 논리삭제
