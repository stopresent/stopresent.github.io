---
title: "it 취업을 위한 알고리즘 문제풀이 입문 : 35. Special Sort(구글 인터뷰)"

categories:
  - BaekJun
tags:
  - BaekJun

author_profile: false

sidebar:
  nav: "docs"

date: 2023-12-07
last_modified_at: 2023-12-07
---


<br>

## Special Sort(구글 인터뷰)

<br>


N개의 정수가 입력되면 당신은 입력된 값을 정렬해야 한다.  

음의 정수는 앞쪽에 양의정수는 뒷쪽에 있어야 한다.   
또한 양의정수와 음의정수의 순서에는 변함이 없어야 한다.


```
▣ 입력설명
첫 번째 줄에 정수 N(5<=N<=100)이 주어지고, 그 다음 줄부터 음수를 포함한 정수가 주어진다. 
숫자 0은 입력되지 않는다.

▣ 출력설명
정렬된 결과를 출력한다.

▣ 입력예제 1 
8
1 2 3 -3 -2 5 6 -6

▣ 출력예제 1
-3 -2 -6 1 2 3 5 6
```


---

<br>

## 풀이  

```cpp
void solve()
{
	int n;
	cin >> n;

	vector<int> minusSeq;
	vector<int> plusSeq;

	int temp;
	for (int i = 0; i < n; ++i)
	{
		cin >> temp;
		if (temp < 0)
			minusSeq.push_back(temp);
		else
			plusSeq.push_back(temp);
	}
	
	for (int i = 0; i < minusSeq.size(); ++i)
		cout << minusSeq[i] << " ";

	for (int i = 0; i < plusSeq.size(); ++i)
		cout << plusSeq[i] << " ";
}
```

<b>전체 코드

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <stdio.h>
#include <string.h>
#include <ctype.h>
#include<iostream>
#include <fstream>
#include <vector>

using namespace std;

void solve()
{
	int n;
	cin >> n;

	vector<int> minusSeq;
	vector<int> plusSeq;

	int temp;
	for (int i = 0; i < n; ++i)
	{
		cin >> temp;
		if (temp < 0)
			minusSeq.push_back(temp);
		else
			plusSeq.push_back(temp);
	}
	
	for (int i = 0; i < minusSeq.size(); ++i)
		cout << minusSeq[i] << " ";

	for (int i = 0; i < plusSeq.size(); ++i)
		cout << plusSeq[i] << " ";
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