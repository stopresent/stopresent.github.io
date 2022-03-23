---
layout: single
title: "Rookiss Part6 SQL 웹서버 : TRANSATION"
categories: DB
tags: DB
author_profile: false
sidebar:
  nav: "docs"
---


## 🙇‍♀️TRANSATION


원자적인 일처리!

락과 비슷하다고 생각하면 됨


### 🪐TRANSATION 사용


**TRANSATION**


* TRAN 명시하지 않으면, 자동으로 COMMIT
`INSERT INTO accounts VALUES(1, 'present', 100, GETUTCDATE(), GETUTCDATE());`



**BEGIN, COMMIT, ROLLBACK**


* BEGIN TRAN; BEGIN TRANSACTION; (임시 저장소)
* COMMIT; (보내기)
* ROLLBACK; (파기)

* 원자적으로 처리해야할때 사용
* ALL OR NOTHING

* 성공/실패 여부에 따라 COMMIT (=COMMIT을 수동으로 하겠다)
```
BEGIN TRAN
INSERT INTO accounts VALUES(2, 'present2', 100, GETUTCDATE(), GETUTCDATE());
ROLLBACK;
```

```
BEGIN TRAN
INSERT INTO accounts VALUES(2, 'present2', 100, GETUTCDATE(), GETUTCDATE());
COMMIT
```


**응용**

```
BEGIN TRY
	BEGIN TRAN
		INSERT INTO accounts VALUES(1, 'present1', 100, GETUTCDATE(), GETUTCDATE());
		INSERT INTO accounts VALUES(3, 'present3', 100, GETUTCDATE(), GETUTCDATE());
	COMMIT
END TRY
BEGIN CATCH
	IF @@TRANCOUNT > 0  -- 현재 활성화된 트랜잭션 수를 반환
		ROLLBACK
	PRINT('ROLLBACK!')
END CATCH
```

  
### 🪐TRAN 사용할 때 주의할 점


* TRAN 안에는 꼭!! 원자적으로 실행될 애들만 넣자
* C# List<Player> List<Salary> 원자적으로 수정
  
  => lock을 잡고 실행
  
  => writelock (상호배타적인 락) readlock (공유 락)
 

BEGIN TRAN으로 TRAN을 시작하고 COMMIT 또는 ROLLBACK을 해주지 않고 다른 작업을 한다면 먹통

LOCK을 한 상태와 비슷함

  
  
