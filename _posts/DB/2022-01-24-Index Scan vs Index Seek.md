---
layout: single
title: "Index Scan vs Index Seek"
categories: DB
tags: DB
author_profile: false
sidebar:
  nav: "docs"
---


## 🙇‍♀️Index Scan vs Index Seek


Random Acess : 한 건 읽기 위해 한 페이지씩 접근
Bookmark Lookup : RID를 통해 행을 찾는다


### 🪐세팅


* 인덱스 접근 방식 (Access)
* Index Scan vs Index Seek

- 테스트 테이블 추가

```
CREATE TABLE TestAccess
(
	id INT NOT NULL,
	name NCHAR(50) NOT NULL,
	dummy NCHAR(1000) NOT NULL
);
GO
```
cf) NCHAR는 고정 값으로 ()만큼 영역을 차지하는 것, 일부러 영역을 많이 차지하게 만들어 페이지를 늘리는 중


- CLUSTERED INDEX와 NONCLUSTERED INDEX 추가

```
CREATE CLUSTERED INDEX TestAccess_CI
ON TestAccess(id);
GO

CREATE NONCLUSTERED INDEX TestAccess_NCI
ON TestAccess(name);
GO
```


- 데이터 집어 넣기

```
DECLARE @i INT;
SET @i = 1;

WHILE (@i <= 500)
BEGIN
	INSERT INTO TestAccess
	VALUES (@i, 'Name' + CONVERT(VARCHAR, @i), 'Hello World' + CONVERT(VARCHAR, @i));
	SET @i = @i + 1;
END
```


- 인덱스 정보

`EXEC sp_helpindex 'TestAccess';`


- 인덱스 번호

```
SELECT index_id, name
FROM sys.indexes
WHERE object_id = object_id('TestAccess');
```


- 조회

```
DBCC IND('Northwind', 'TestAccess', 1); /*CLUSTERED*/
DBCC IND('Northwind', 'TestAccess', 2); /*NONCLUSTERED*/
```

- CLUSTERED INDEX, NONCLUSTERED INDEX 가시적 표현

```
-- CLUSTERED(1) : id
--			825
-- 824 82 827 ~ 9095(167)

-- NONCLUSTERED(2) : name
--			8921
-- 8920 8925 ~ 8922(13)
```


### 🪐SCAN SEEK KEY LOOKUP


**논리적 읽기 -> 실제 데이터를 찾기 위해 읽은 페이지 수**

```
SET STATISTICS TIME ON; /* 걸린 시간*/
SET STATISTICS IO ON; /*페이지 수*/
```


**CLUSTERED**


- INDEX SCAN = LEAF PAGE 순차적으로 검색

```
SELECT *
FROM TestAccess;
```


- INDEX SEEK

```
SELECT *
FROM TestAccess
WHERE id = 104;
```


**NONCLUSTERED**


- INDEX SEEK + KEY LOOKUP

```
SELECT *
FROM TestAccess
WHERE name = 'name5'
```


- INDEX SCAN + KEY LOOKUP

```
SELECT TOP 10 *
FROM TestAccess
ORDER BY name;
```



