---
title: "C++ Rookiss Part3 자료구조와 알고리즘 : QuickSort"

categories:
  - Algorithm
tags:
  - Algorithm

author_profile: false

sidebar:
  nav: "docs"

date: 2022-06-28
last_modified_at: 2022-06-28
---

<br>

## 🙇‍♀️QuickSort


<br>


### 🪐QuickSort


```cpp
#include <iostream>
#include <vector>
#include <list>
#include <stack>
#include <queue>
using namespace std;

// QuickSort

int partition(vector<int>& v, int left, int right)
{
	int pivot = v[left];
	int low = left + 1;
	int high = right;

	while (low <= high)
	{
		while (low <= right && pivot >= v[low])
			low++;

		while (high >= left + 1 && pivot <= v[high])
			high--;

		if (low < high)
			swap(v[low], v[high]);
	}

	swap(v[left], v[high]);
	return high;
}

void QuickSort(vector<int>& v, int left, int right)
{
	if (left > right)
		return;

	int pivot = partition(v, left, right);
	QuickSort(v, left, pivot - 1);
	QuickSort(v, pivot + 1, right);
}

int main()
{
	vector<int> v;

	srand(time(0));

	for (int i = 0; i < 50; i++)
	{
		int randValue = rand() % 100;
		v.push_back(randValue);
	}

	QuickSort(v, 0, v.size() - 1);

	for (int i : v)
		cout << i << endl;
}
```