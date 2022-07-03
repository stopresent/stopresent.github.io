---
title: "C++ Rookiss Part3 자료구조와 알고리즘 : DYNAMIC PROGRAMMING"

categories:
  - Algorithm
tags:
  - Algorithm

author_profile: false

sidebar:
  nav: "docs"

date: 2022-07-01
last_modified_at: 2022-07-01
---

<br>

## 🙇‍♀️DYNAMIC PROGRAMMING

<br>


### 🪐DYNAMIC PROGRAMMING

동적 계획법 : DP

* COMBINATION(이항계수)
    - 상자 안에 공 5개. 공 2개를 뽑는 경우의 수?
    - nCk = n!/k!(n-k)!

```cpp
// 이항계수 구현

#include <iostream>
#include <vector>
#include <list>
#include <stack>
#include <queue>
using namespace std;
#include <thread>
#include <Windows.h>

int cache[50][50];

int combination(int n, int r)
{
    // 기저 사례
    if (r == 0 || n == r)
        return 1;

    // 이미 답을 구한적이 있으면 답을 바로 반환
    int& ret = cache[n][r];
    if (ret != -1)
        return ret;

    // 답을 캐쉬에 저장
    return ret = combination(n - 1, r - 1) + combination(n - 1, r);
}

int main()
{
    __int64 start = GetTickCount64();

    int lotto = combination(45, 6);

    __int64 end = GetTickCount64();

    cout << end - start << "ms" << endl;
}
```

캐쉬에 저장해서 속도를 높혔다.

메모리를 주고 속도를 취하는 전략!


