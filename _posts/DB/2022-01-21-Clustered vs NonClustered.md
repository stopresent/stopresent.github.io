---
layout: single
title: "Clustered vs NonClustered"
categories: DB
tags: DB
author_profile: false
sidebar:
  nav: "docs"
---

## 🙇‍♀️Clustered vs NonClustered


- 인덱스 종류
- Clustered(영한 사전) vs Non-Clustered(색인)

**Clustered**
- Leaf Page = Data Page 
- 데이터는 Clustered Index 키 순서로 정렬

**Non_Clustered ? (사실 Clustered Index 유무에 따라서 다르게 동작)**
- 1) Clustered Index가 없는 경우
	- Clustered Index가 없으면 데이터는 Heap Table이라는 곳에 저장
	- Heap RID -> Heap Table에 접근 데이터 추출
- 2) Clustered Index가 있는 경우
	- Heap Table이 없음. Leaf Table에 실제 데이터가 있다
	- Clustered Index의 실제 키 값을 들고 있는다
  
  
  
### 🪐세팅


- 임시 데스트 테이블을 만들고 데이터 복사

```
SELECT *
INTO TestOrderDetails
FROM [Order Details];

SELECT *
FROM TestOrderDetails;
```


- 인덱스 추가

```
CREATE INDEX Index_OrderDetails
ON TestOrderDetails(OrderID, ProductID);
```


- 인덱스 정보

`EXEC sp_helpindex 'TestOrderDetails'`


- 인덱스 번호 찾기

```
SELECT index_id, name
FROM sys.indexes
WHERE object_id = object_id('TestOrderDetails')
```


### 🪐Non-Clustered조회


- PageType 1(DATA PAGE) 2(INDEX PAGE)

`DBCC IND('Northwind', 'TestOrderDetails', 2);`
```
-			 904
- 832 872 873 874 875 876
```


- Heap RID ([페이지 주소(4)][파일ID(2)][슬롯(2)] ROW)
- Heap Table[ {Page} {Page} {Page} {Page} ]

`DBCC PAGE('Northwind', 1, 832, 3);`



### 🪐Clustered조회


- Clustered 인덱스 추가

```
CREATE CLUSTERED INDEX Index_OrderDetails_Clustered
ON TestOrderDetails(OrderID);
```


- Non-Clustered

`DBCC PAGE('Northwind', 1, 936, 3);`


- 조회
- PageType 1(DATA PAGE) 2(INDEX PAGE)

`DBCC IND('Northwind', 'TestOrderDetails', 1);`
```
- 928
- 880 888 889 890 ~ 896
```



처음부터 다시 해보면서 공부해야할듯 이해가 잘 안됨;;
