---
title: "it 취업을 위한 알고리즘 문제풀이 입문 : 83. 복면산 SEND+MORE=MONEY (MS인터뷰)"

categories:
  - BaekJun
tags:
  - BaekJun

author_profile: false

sidebar:
  nav: "docs"

date: 2023-12-30
last_modified_at: 2023-12-30
---

# 🙇‍♀️복면산 SEND+MORE=MONEY (MS인터뷰)

SEND+MORE=MONEY 라는 유명한 복면산이 있습니다.  

이 복면산을 구하는 프로그램을 작성하세요.  

**출력형태**
```
  9 5 6 7
+ 1 0 8 5
---------
1 0 6 5 2
```

## 🚀풀이

DFS를 이용하는 문제.  

SEND, MORE, MONEY는 총 8개의 알파벳이 사용된다.  

각 알파벳에 모든 숫자 경우를 집어넣고 완성되는 경우를 찾는다.  

```cpp
int Send()
{
	return arr[6] * 1000 + arr[1] * 100 + arr[3] * 10 + arr[0] * 1;
}

int More()
{
	return arr[2] * 1000 + arr[4] * 100 + arr[5] * 10 + arr[1] * 1;
}

int Money()
{
	return arr[2] * 10000 + arr[4] * 1000 + arr[3] * 100 + arr[1] * 10 + arr[7] * 1;
}
```
그 후 이렇게 함수를 파서 정답이 되는 경우를 찾아야하는데 이 때 DFS를 사용한다.  

```cpp
void DFS(int L)
{
	if (L == 8)
	{
		if (arr[6] == 0 || arr[2] == 0)
			return;
		if (Send() + More() == Money())
		{
			cout << "  " << arr[6] << " " << arr[1] << " " << arr[3] << " " << arr[0] << '\n';
			cout << "+ " << arr[2] << " " << arr[4] << " " << arr[5] << " " << arr[1] << '\n';
			cout << "----------" << '\n';
			cout << arr[2] << " " << arr[4] << " " << arr[3] << " " << arr[1] << " " << arr[7] << '\n';
		}
	}
	else
	{
		for (int i = 0; i < 10; ++i)
		{
			if (ch[i] == 0)
			{
				arr[L] = i;
				ch[i] = 1;
				DFS(L + 1);
				ch[i] = 0;
			}
		}
	}
}
```
레벨이 8이 아니면 계속 DFS를 돌리는데 0~9 모든 경우를 대입해야하므로 for문을 돌리면서 DFS를 돌린다.  

이렇게 하고 DFS를 레벨 0부터 시작하게 실행한다면 아래와 같은 결과가 나온다.  

![복면산](https://github.com/stopresent/BOJ/assets/86364202/cdc3bb98-af7f-43d4-bc4d-d742cc8bfedf)  


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

int arr[8], ch[8];

int Send()
{
	return arr[6] * 1000 + arr[1] * 100 + arr[3] * 10 + arr[0] * 1;
}

int More()
{
	return arr[2] * 1000 + arr[4] * 100 + arr[5] * 10 + arr[1] * 1;
}

int Money()
{
	return arr[2] * 10000 + arr[4] * 1000 + arr[3] * 100 + arr[1] * 10 + arr[7] * 1;
}

void DFS(int L)
{
	if (L == 8)
	{
		if (arr[6] == 0 || arr[2] == 0)
			return;
		if (Send() + More() == Money())
		{
			cout << "  " << arr[6] << " " << arr[1] << " " << arr[3] << " " << arr[0] << '\n';
			cout << "+ " << arr[2] << " " << arr[4] << " " << arr[5] << " " << arr[1] << '\n';
			cout << "----------" << '\n';
			cout << arr[2] << " " << arr[4] << " " << arr[3] << " " << arr[1] << " " << arr[7] << '\n';
		}
	}
	else
	{
		for (int i = 0; i < 10; ++i)
		{
			if (ch[i] == 0)
			{
				arr[L] = i;
				ch[i] = 1;
				DFS(L + 1);
				ch[i] = 0;
			}
		}
	}
}

int main() 
{
	ios_base::sync_with_stdio(false);
	cin.tie(NULL);
	cout.tie(NULL);
	freopen("input.txt", "rt", stdin);

	DFS(0);

	return 0;
}
```