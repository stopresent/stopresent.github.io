---
title: "Union & Find"

categories:
  - Algorithm
tags:
  - Algorithm

author_profile: false

sidebar:
  nav: "docs"

date: 2023-12-29
last_modified_at: 2023-12-29
---

# 🙇‍♀️Union & Find

disjoinset 알고리즘을 사용한다.  

예를 들어  
집합 A : {1, 2}  
집합 B : {2, 3}  
이 있다고 할 때, 
2라는 공통 원소가 있으므로 {1, 2, 3}으로 표현하는 방식이다.  

이 알고리즘은 아래의 문제와 함께 설명!  

```
77. 친구인가? (Union&Find 자료구조)  

오늘은 새 학기 새로운 반에서 처음 시작하는 날이다. 

현수네 반 학생은 N명이다. 

현수는 각 학생들의 친구관계를 알고 싶다.

모든 학생은 1부터 N까지 번호가 부여되어 있고, 현수에게는 각각 두 명의 학생은 친구 관계가 번호로 표현된 숫자쌍이 주어진다. 

만약 (1, 2), (2, 3), (3, 4)의 숫자쌍이 주어지면 1번 학생과 2번 학생이 친구이고, 2번 학생과 3번 학생이 친구, 3번 학생과 4번 학생이 친구이다. 

그리고 1번 학생과 4번 학생은 2번과 3번을 통해서 친구관계가 된다.

학생의 친구관계를 나타내는 숫자쌍이 주어지면 특정 두 명이 친구인지를 판별하는 프로그램을 작성하세요. 

두 학생이 친구이면 “YES"이고, 아니면 ”NO"를 출력한다.


▣ 입력설명
첫 번째 줄에 반 학생수인 자연수 N(1<=N<=1,000)과 숫자쌍의 개수인 M(1<=M<=3,000)이 주어지고, 다음 M개의 줄에 걸쳐 숫자쌍이 주어진다. 

마지막 줄에는 두 학생이 친구인지 확인하는 숫자쌍이 주어진다.

▣ 출력설명
첫 번째 줄에 “YES"또는 "NO"를 출력한다.

▣ 입력예제 1 
9 7
1 2
2 3
3 4
4 5
6 7
7 8
8 9
3 8

▣ 출력예제 1
NO
```

위의 문제를 보면 딱 설명에 맞는 경우이다.  

위 문제의 해결 코드는 아래와 같다.  

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <stdio.h>
#include <string.h>
#include <ctype.h>
#include<iostream>
#include <fstream>
#include <vector>
#include <algorithm>

using namespace std;
int n, m, a, b;
int unf[1001];

int Find(int v)
{
	if (v == unf[v])
		return v;
	else
		return unf[v] = Find(unf[v]);
}

void Union(int a, int b)
{
	a = Find(a);
	b = Find(b);
	if (a != b) unf[a] = b;
}

void solve()
{
	cin >> n >> m;
	for (int i = 1; i <= n; ++i)
		unf[i] = i;

	for (int i = 1; i <= m; ++i)
	{
		cin >> a >> b;
		Union(a, b);
	}

	int fa, fb;
	cin >> a >> b;
	fa = Find(a);
	fb = Find(b);
	if (fa == fb)
		cout << "YES" << endl;
	else
		cout << "NO" << endl;
}

int main() 
{
	ios_base::sync_with_stdio(false);
	cin.tie(NULL);
	cout.tie(NULL);
	freopen("input.txt", "rt", stdin);

	solve();

	return 0;
}
```

분석하기!  

**Find**
```cpp
int Find(int v)
{
	if (v == unf[v])
		return v;
	else
		return Find(unf[v]);
}
```
이렇게 로직을 짠다면 계속해서 꼬리에 꼬리를 무는 형식으로 Find가 이루어진다.  

그래도 같은 그룹끼리 묶인다.  

**Find**
```cpp
int Find(int v)
{
	if (v == unf[v])
		return v;
	else
		return unf[v] = Find(unf[v]);
}
```
이런식으로 짜게 된다면 메모이제이션이된다.  
`unf[v] = Find(unf[v]);`이 부분이 최상단 부분과 바로 연결하게 만든다.  

**Union**
```cpp
void Union(int a, int b)
{
	a = Find(a);
	b = Find(b);
	if (a != b) unf[a] = b;
}
```
Union은 같은 그룹으로 만드는 함수이다. b가 머리 a가 꼬리를 맡게되는 함수.  

문제 해결 함수 분석!
```cpp
void solve()
{
	cin >> n >> m;
	for (int i = 1; i <= n; ++i)
		unf[i] = i; // 자기 자신이 함수이다.

	for (int i = 1; i <= m; ++i)
	{
		cin >> a >> b;
		Union(a, b); // b가 머리가되고 a가 꼬리가되며 함수 합치기
	}

	int fa, fb;
	cin >> a >> b;
	fa = Find(a);
	fb = Find(b);
	if (fa == fb)
		cout << "YES" << endl;
	else
		cout << "NO" << endl;
}
```