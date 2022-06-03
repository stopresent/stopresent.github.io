---
title: "C++ Rookiss Part1 C++ 프로그래밍 입문 : Algorithm"

categories:
  - CPP
tags:
  - CPP

author_profile: false

sidebar:
  nav: "docs"

date: 2022-06-03
last_modified_at: 2022-06-03
---

<br>

## 🙇‍♀️Algorithm


<br>


## 🪐Algorithm


* 자료구조 (데이터를 저장하는 구조)
* 알고리즘 (데이터를 어떻게 사용할 것인가?)

    * find / find_of
    * count / count_if / all_of / any_of / none_of / for_each
    * remove / remove_if

```cpp
vector<int>::iterator itFind = find(v.begin(), v.end(), number); // number 숫자 벡터에 체크


vector<int>::iterator itFind = find_if(v.begin(), v.end(), [](int n) { return (n % 11) == 0; });



int n = count_if(v.begin(), v.end(), [](int n) { return (n % 2) != 0; });



// 모든 데이터가 홀수입니까?
bool b1 = all_of(v.begin(), v.end(), [](int n) { return (n % 2) != 0; });
// 홀수인 데이터가 하나라도 있습니까?
bool  b2 = any_of(v.begin(), v.end(), [](int n) { return (n % 2) != 0; });
// 모든 데이터가 홀수가 아닙니까?
bool b3 = none_of(v.begin(), v.end(), [](int n) { return (n % 2) != 0; });



for_each(v.begin(), v.end(), [](int& n) { n = n * 3; });



// 홀수인 데이터를 일괄 삭제
v.erase(remove_if(v.begin(), v.end(), IsOdd()), v.end());
```