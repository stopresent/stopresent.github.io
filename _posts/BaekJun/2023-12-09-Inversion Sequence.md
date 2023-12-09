---
title: "it 취업을 위한 알고리즘 문제풀이 입문 : 38. Inversion Sequence"

categories:
  - BaekJun
tags:
  - BaekJun

author_profile: false

sidebar:
  nav: "docs"

date: 2023-12-09
last_modified_at: 2022-12-09
---

# 🙇‍♀️Inversion Sequence

1부터 n까지의 수를 한 번씩만 사용하여 이루어진 수열이 있을 때, 1부터 n까지 각각의 수 앞에 놓여 있는 자신보다 큰 수들의 개수를 수열로 표현한 것을 Inversion Sequence라 한다.  

예를 들어 다음과 같은 수열의 경우  
 4 8 6 2 5 1 3 7  
1앞에 놓인 1보다 큰 수는 4, 8, 6, 2, 5. 이렇게 5개이고,  
2앞에 놓인 2보다 큰 수는 4, 8, 6. 이렇게 3개,  
3앞에 놓인 3보다 큰 수는 4, 8, 6, 5 이렇게 4개......  
따라서 4 8 6 2 5 1 3 7의 inversion sequence는 5 3 4 0 2 1 1 0 이 된다.  

n과 1부터 n까지의 수를 사용하여 이루어진 수열의 inversion sequence가 주어졌을 때,  
원래의 수열을 출력하는 프로그램을 작성하세요.  

```
▣ 입력설명
첫 번째 줄에 자연수 N(3<=N<100)이 주어지고, 
두 번째 줄에는 inversion sequence가 숫자 사이에 한 칸의 공백을 두고 주어진다.

▣ 출력설명
오름차순으로 정렬된 수열을 출력합니다.

▣ 입력예제 1 
8
5 3 4 0 2 1 1 0

▣ 출력예제 1
4 8 6 2 5 1 3 7
```

---

## 🚀풀이

inversion sequence를 담는 vector와 결과를 담는 vector를 나누어서 풀었다.  
순회를 제일 끝부터 처음까지로 돌면서 inversion sequence의 값 만큼 추가로 순회하면서 res vector안의 내용을 앞으로 옮겨주었다.  

코드를 보면 쉬울거같다.  

```cpp
int N;

void solve()
{
	cin >> N;
	vector<int> seq(N);
	vector<int> res(N);

	for (int i = 0; i < N; ++i)
	{
		cin >> seq[i];
	}

	for (int i = N - 1; i >= 0; --i)
	{
		int pos = i;
		for (int j = i; j < i + seq[i]; ++j)
		{
			res[j] = res[j + 1];
			pos = j + 1;
		}
		res[pos] = i + 1;
	}

	for (int i = 0; i < N; ++i)
		cout << res[i] << " ";
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

using namespace std;

int N;

void solve()
{
	cin >> N;
	vector<int> seq(N);
	vector<int> res(N);

	for (int i = 0; i < N; ++i)
	{
		cin >> seq[i];
	}

	for (int i = N - 1; i >= 0; --i)
	{
		int pos = i;
		for (int j = i; j < i + seq[i]; ++j)
		{
			res[j] = res[j + 1];
			pos = j + 1;
		}
		res[pos] = i + 1;
	}

	for (int i = 0; i < N; ++i)
		cout << res[i] << " ";
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