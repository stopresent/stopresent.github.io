---
title: "it 취업을 위한 알고리즘 문제풀이 입문 : 52. Ugly Numbers"

categories:
  - BaekJun
tags:
  - BaekJun

author_profile: false

sidebar:
  nav: "docs"

date: 2023-12-22
last_modified_at: 2023-12-22
---

# 🙇‍♀️Ugly Numbers

어떤 수를 소인수분해 했을 때 그 소인수가 2 또는 3 또는 5로만 이루어진 수를 Ugly Number라고 부릅니다.  
Ugly Number를 차례대로 적어보면  
1, 2, 3, 4, 5, 6, 8, 9, 10, 12, 15, .......입니다.  
숫자 1은 Ugly Number의 첫 번째 수로 합니다.  
자연수 N이 주어지면 Ugly Number를 차례로 적을 때 N번째 Ugly Number를 구하는 프로그램을 작성하세요.  

```
▣ 입력설명
첫 줄에 자연수 N(3<=N<=1500)이 주어집니다. 

▣ 출력설명
첫 줄에 N번째 Ugly Number를 출력하세요.

▣ 입력예제 1 
10

▣ 출력예제 1
12

▣ 입력예제 2 
1500

▣ 출력예제 2
859963392
```

## 🚀풀이

이전의 투포인트 알고리즘을 응용하여 쓰리포인트로 이용하여 문제를 푼다.  

포인트는 2, 3, 5로 3개이다.  

문제 해결 로직은 다음과 같다.  

먼저 ugly numbers를 담을 배열을 만든다.  
`arr[1501]`  

`p2 = p3 = p5 = 1`로 초기화 한다.  

각 포인터에서 해당하는 숫자를 곱하면서 비교를 시작하는데 코드를 보면 이해가 쉽다.  

```cpp
int n, p2, p3, p5, minNum = 123456789;
int arr[1501];

void solve()
{
	cin >> n;
	arr[1] = 1;
	p2 = p3 = p5 = 1;

	for (int i = 2; i <= n; ++i)
	{
        // 값을 곱해주면서 최소값을 찾는다. 
        // 이 최소값이 ugly numbers에 채워지게된다.
		minNum = min(arr[p2] * 2, arr[p3] * 3);
		minNum = min(minNum, arr[p5] * 5);

        // 최소값에 해당되는 포인터인 경우 포인터를 증가시켜준다. 
        // 증가함에 따라서 값이 중복되지 않고 증가하게 된다.
		if (arr[p2] * 2 == minNum)
			p2++;
		if (arr[p3] * 3 == minNum)
			p3++;
		if (arr[p5] * 5 == minNum)
			p5++;
		arr[i] = minNum;
	}

	cout << arr[n] << '\n';
}
```

## 🚀전체 코드

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

int n, p2, p3, p5, minNum = 123456789;
int arr[1501];

void solve()
{
	cin >> n;
	arr[1] = 1;
	p2 = p3 = p5 = 1;

	for (int i = 2; i <= n; ++i)
	{
		minNum = min(arr[p2] * 2, arr[p3] * 3);
		minNum = min(minNum, arr[p5] * 5);
		if (arr[p2] * 2 == minNum)
			p2++;
		if (arr[p3] * 3 == minNum)
			p3++;
		if (arr[p5] * 5 == minNum)
			p5++;
		arr[i] = minNum;
	}

	cout << arr[n] << '\n';
}

int main() 
{
	ios_base::sync_with_stdio(false);
	cin.tie(NULL);
	cout.tie(NULL);
	//freopen("input.txt", "rt", stdin);

	solve();

	return 0;
}
```