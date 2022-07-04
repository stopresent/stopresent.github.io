---
title: "C++ Rookiss Part3 자료구조와 알고리즘 : TrianglePath"

categories:
  - Algorithm
tags:
  - Algorithm

author_profile: false

sidebar:
  nav: "docs"

date: 2022-07-04
last_modified_at: 2022-07-04
---

<br>

## 🙇‍♀️TrianglePath

<br>


### 🪐TrianglePath


```cpp
#include <iostream>
#include <vector>
#include <list>
#include <stack>
#include <queue>
using namespace std;
#include <thread>
#include <windows.h>


// 오늘의 주제 : TrianglePath
// - (0,0)에서 시작해서 아래 or 우측 아래로 이동 가능
// - 만나는 숫자를 모두 더함
// - 최대가 되는 경로와 더한 값?

// 6
// 1 2
// 3 7 4
// 9 4 1 7
// 2 7 5 9 4

int N;
vector<vector<int>> board;
vector<vector<int>> cache;
vector<vector<int>> nextX;

int path(int y, int x)
{
	// 기저 사항
	if (y == N)
		return 0;

	// 캐시 확인
	int& ret = cache[y][x];
	if (ret != -1)
		return ret;

	{
		// 경로 기록
		int nextBottom = path(y + 1, x);
		int nextBottomRight = path(y + 1, x + 1);
		if (nextBottom > nextBottomRight)
			nextX[y][x] = x;
		else
			nextX[y][x] = x + 1;
	}

	// 적용
	//ret = cache[y][x];
	//ret += max(ret, cache[y + 1][x]);
	//ret += max(ret, cache[y + 1][x + 1]);

	return ret = board[y][x] + max(path(y + 1, x), path(y + 1, x + 1));
}

int main()
{
	board = vector<vector<int>>
	{
		{6},
		{1, 2},
		{3, 7, 4},
		{9, 4, 1, 7},
		{2, 7, 5, 9, 4}
	};

	N = board.size();
	cache = vector<vector<int>>(N, vector<int>(N, -1));
	nextX = vector<vector<int>>(N, vector<int>(N));

	int ret = path(0, 0);
	cout << ret << endl;

	// 경로 만들기
	int y = 0;
	int x = 0;

	while (y < N)
	{
		if (y == N - 1)
		{
			cout << board[y][x];
			break;
		}
		cout << board[y][x] << " -> ";

		x = nextX[y][x];
		y++;
	}
}
```