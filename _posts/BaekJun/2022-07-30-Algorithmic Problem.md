---
title: "BaekJun Programing : 종만북 문제"

categories:
  - BaekJun
tags:
  - BaekJun

author_profile: false

sidebar:
  nav: "docs"

date: 2022-07-30
last_modified_at: 2022-07-30
---

<br>

## 🙇‍♀️종만북


<br>

### 🪐종만북 문제

```cpp
// 주어진 배열 A에서 가장 많이 등장하는 숫자를 반환한다.
// 만약 두 가지 이상 있을 경우 아무것이나 반환한다.
int majority1(const vector<int>& A)
{
	int N = A.size();
	int majority = -1, majorityCount = 0;
	for (int i = 0; i < N; ++i)
	{
		int V = A[i], count = 0;
		for (int j = 0; j < N; ++j)
		{
			if (A[j] == V)
				count++;
		}
		if (count > majority)
		{
			majority = V;
			majorityCount = count;
		}
	}
	return majority;
}
```

```cpp
// A의 각 원소가 0부터 100 사이의 값일 경우 가장 많이 등장하는 숫자를 반환한다.
int majority2(const vector<int>& A)
{
	int N = A.size();
	vector<int> count(101, 0);
	for (int i = 0; i < N; ++i)
		count[A[i]]++;
	// 지금까지 확인한 숫자 중 빈도수가 제일 큰 것을 majority에 저장한다.
	int majority = 0;
	for (int i = 0; i <= 100; ++i)
	{
		if (majority < count[i])
		{
			majority = i;
		}
	}
	return majority;
}
```

```cpp
// 실수 배열A가 주어질 때, 각 위치에서의 M-이동 평균 값을 구한다.
vector<double> movingAverage1(vector<double>& A, int M)
{
	vector<double> ret;
	int N = A.size();
	for (int i = M - 1; i < N; ++i)
	{
		// A[i]까지의 이동 평균을 구하자.
		double partialSum = 0;
		for (int j = 0; j < M; ++j)
			partialSum += A[i - j];
		ret.push_back(partialSum / M);
	}
	return ret;
}
```

```cpp
// 실수 배열A가 주어질 때, 각 위치에서의 M-이동 평균 값을 구한다.
vector<double> movingAverage2(vector<double>& A, int M)
{
	vector<double> ret;
	int N = A.size();
	double partialSum = 0;
	for (int i = 0; i < M - 1; ++i)
		partialSum += A[i];
	for (int i = M - 1; i < N; ++i)
	{
		partialSum += A[i];
		ret.push_back(partialSum / M);
		partialSum -= A[i - M + 1];
	}
	return ret;
}
```

```cpp
// 음식 메뉴 정하기
const int INF = 987654321;
// 이 메뉴로 모두가 식사할 수 있는지 여부를 확인한다.
bool canEverybodyEat(const vector<int>& menu);
// 요리할 수 있는 음식의 종류 수
int M;
// food 번째 음식을 만들지 여부를 결정한다.
int selectMenu(vector<int>& menu, int food)
{
	// 기저사례 : 모든 음식에 대해 만들지 여부를 결정했을 때
	if (food = M)
	{
		if (canEverybodyEat(menu)) return menu.size();
		// 아무것도 못 먹는 사람이 있으면 아주 큰 값을 반환한다.
		return INF;
	}
	// 이 음식을 만들지 않는 경우의 답을 계산한다.
	int ret = selectMenu(menu, food + 1);
	// 이 음식을 만드는 경우의 답을 계산해서 더 작은 것을 취한다.
	menu.push_back(food);
	ret = min(ret, selectMenu(menu, food + 1));
	menu.pop_back();
	return ret;
}
```

```cpp
// 자연수 n의 소인수 분해 결과를 담은 정수 배열을 반환한다.
vector<int> factor(int n)
{
	if (n == 1) return vector<int>(1, 1); // n이 1일 경우에는 예외
	vector<int> ret;
	for (int div = 2; n > 1; ++div)
	{
		while (n % div == 0)
		{
			n /= div;
			ret.push_back(div);
		}
	}
	return ret;
}
```


```cpp
// array[i] = element인 첫 i를 반환한다. 없으면 -1을 반환한다.
int firstIndex(const vector<int>& array, int element)
{
	for (int i = 0; i < array.size(); ++i)
	{
		if (array[i] == element)
			return i;
	}
	return -1;
}
```

```cpp
// 선택 정렬과 삽입 정렬
void selectionSort(vector<int>& A)
{
	for (int i = 0; i < A.size(); ++i)
	{
		int minIndex = i;
		for (int j = i + 1; j < A.size(); ++j)
			if (A[minIndex] > A[j])
				minIndex = j;
		swap(A[i], A[minIndex]);
	}
}
void insertionSort(vector<int>& A)
{
	for (int i = 0; i < A.size(); ++i)
	{
		// A[0..i-1]에 A[i]를 끼워넣는다.
		int j = i;
		while (j > 0 && A[j - 1] > A[j])
		{
			swap(A[j - 1], A[j]);
			--j;
		}
	}
}
```

```cpp
// 최대 연속 부분 구간 합 문제를 푸는 무식한 알고리즘들
const int MIN = numeric_limits<int>::min();
// A[]의 연속된 부분 구간의 최대 합을 구한다. 시간 복잡도: O(N^3)
int inefficientMaxSum(const vector<int>& A)
{
	int ret = MIN, N = A.size();
	for (int i = 0; i < N; ++i)
	{
		for (int j = i; j < N; ++j)
		{
			// 구간 A[i,j]의 합을 구한다.
			int sum = 0;
			for (int k = i; k <= j; ++k)
				sum += A[k];
			ret = max(ret, sum);
		}
	}
	return ret;
}

// A[]의 연속된 부분 구간의 최대 합을 구한다. 시간 복잡도: O(N^2)
int betterMaxSum(const vector<int>& A)
{
	int ret = MIN, N = A.size();
	for (int i = 0; i < N; ++i)
	{
		int sum = 0;
		for (int j = i; j < N; ++j)
		{
			sum += A[j];
			ret = max(ret, sum);
		}
	}
	return ret;
}

// A[lo..hi]의 연속된 부분 구간의 최대 합을 구한다. 시간 복잡도: O(NlogN)
int fastMaxSum(const vector<int>& A, int lo, int hi)
{
	// 기저 사례: 구간 길이가 1일 경우
	if (lo == hi) return A[lo];
	// 배열을 A[lo..mid], A[mid+1..hi]의 두 조각으로 나눈다.
	int mid = (lo + hi) / 2;
	// 두 부분에 모두 걸쳐 있는 최대 합 구간을 찾는다. 이 구간은
	// A[i..mid]와 A[mid+1..j] 형태를 갖는 구간의 합으로 이루어 진다.
	// A[i..mid] 형태를 갖는 최대 구간을 찾는다.
	int left = MIN, right = MIN, sum = 0;
	for (int i = mid; i > lo; --i)
	{
		sum += A[i];
		left = max(left, sum);
	}
	// A[mid+1..j] 형태를 갖는 최대 구간을 찾는다.
	sum = 0;
	for (int j = mid + 1; j < hi; ++j)
	{
		sum += A[j];
		right = max(right, sum);
	}
	// 최대 구간이 두 조각 중 하나에만 속해 있는 경우의 답을 재귀 호출로 찾는다.
	int single = max(fastMaxSum(A, lo, mid), fastMaxSum(A, mid + 1, hi));
	// 두 경우 중 최대치를 반환한다.
	return max(single, left + right);
}

// A[]의 연속된 부분 구간의 최대 합을 구한다. 시간 복잡도: O(N)
int fastestMaxSum(const vector<int>& A)
{
	int N = A.size(), psum = 0, ret = MIN;
	for (int i = 0; i < N; ++i)
	{
		psum = max(psum, 0) + A[i];
		ret = max(ret, psum);
	}
	return ret;
}
```

```cpp
// 필수 조건: A는 오름차순으로 정렬되어 있다.
// 결과: A[i - 1] < x <= A[i]인 i를 반환한다.
// 이 때 A[-1] = 음의 무한대, A[n] = 양의 무한대라고 가정한다.
int binsearch(const vector<int>& A, int x)
{
	int n = A.size();
	int lo = -1, hi = n;
	// 반복문 불변식 1: lo < hi
	// 반복문 불변식 2: A[lo] < x <= A[hi]
	// (*) 불변식은 여기서 성립해야 한다.
	while (lo + 1 < hi)
	{
		int mid = (lo + hi) / 2;
		if (A[mid] < x)
			lo = mid;
		else
			hi = mid;
		// (**) 불변식은 여기서도 성립해야 한다.
	}
	return hi;
}
```

```cpp
int printDecimal(int a, int b)
{
	int iter = 0;
	while (a > 0)
	{
		if (iter++ == 1) cout << ".";
		cout << a / b;
		a = (a % b) * 10;
	}
}
```