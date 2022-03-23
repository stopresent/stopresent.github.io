---
layout: single
title: "Rookiss Part6 SQL 웹서버 : SELECT FROM WHERE"
categories: DB
tags: DB
author_profile: false
sidebar:
  nav: "docs"
---


## 🙇‍♀️SELECT FROM WHERE


- /* */ 여러줄 주석

- -- 한줄짜리 주석


SQL (RDBMS를 조작하기 위한 명령어)
+@ T-SQL


CRUD (Create - Read - Update - Delete)


`USE BaseballData;`로 데이터베이스를 인식하게 해줌

### 🪐SELECT FROM WHERE

* 로직이 순서대로 실행되는 것이 아니다
* 세미콜론은 안붙여도 좋지만 끝을 나타내기 위해 붙이는게 좋다
* 드래그를 한 후 F5를 누르면 그 부분만 실행이 된다



### 🪐WHERE

* WHERE birth = 1866;

= 생년이 1866년만 선택


* WHERE birth != 1866;

= 생년이 1866이 아닌것만 선택


* WHERE birthCountry = 'USA';

= 출생지가 USA인 것만 선택



**논리연산자**
* &&가 아니라 AND라고 적어주어야 됨
* || 은 OR

**AND가 OR보다 우선순위가 높음**

* NULL 비교
* `WHERE deathYear != NULL`
  * 이렇게 사용은 불가
* `WHERE deathYear IS NOT NULL`
  * `IS NOT`키워드를 사용해야 됨



**패턴 매칭**
* `LIKE`키워드를 사용한다!
  * & : 임의의 문자열
    * `WHERE birthCity LIKE 'NEW%'`
  * _ : 임의의 문자 1개
    * `WHERE birthCity LIKE 'NEW Yor_'`
