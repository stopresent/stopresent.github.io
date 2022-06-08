---
title: "C++ Rookiss Part1 C++ 프로그래밍 입문 : ModernCpp"

categories:
  - CPP
tags:
  - CPP

author_profile: false

sidebar:
  nav: "docs"

date: 2022-06-04
last_modified_at: 2022-06-04
---

<br>

## 🙇‍♀️ModernCpp


<br>


## 🪐ModernCpp

<br>

1. auto


- 컴파일러가 rvalue를 보고 타입형을 추측해 준다.
- const, &는 무시한다!


2. nullptr


```cpp
int* ptr = nullptr; // ptr에는 0이 들어가 있음
ptr = NULL; // NULL == 0


// 0 NULL
Test(0);
Test(NULL); // 포인터 버전이 아닌 0인 버전으로 호출 됨
Test(nullptr); // 포인터 버전으로 출력
```


3. using

```cpp
typedef vector<int>::iterator VecIt;

typedef int id;
using id2 = int;

// 1) 직관성
typedef void (*MyFunc);
using MyFunc2 = void(*)();

// 2) 템플릿
//template<typename T>
//using List = std::vector<T>; // typedef으로는 구현 x
```

4. enum class

```cpp
// enum class (scoped enum)
// 1) 이름공간 관리 (scoped)
// 2) 암묵적인 변환 금지


enum class ObjectType
{
	Player,
	Monster,
	Projectile
};
// 이름공간이 enum class 안까지만 유효해서 Player란 이름을 다른 곳에서 만들어서 사용 가능
// 암묵적인 변환이 금지되어서 int input = Player;등이 금지
// 명시적으로 변환은 가능, static_cast<int>(ObjectType::Player)
// 사용은 ObjectType::Player처럼 사용
```


5. delete


delete (삭제된 함수)
더 이상 함수를 사용하지 않을 때 = delete를 붙여서 삭제함


6. override, final


cs에서 virtual한 가상 함수를 자손이 재정의 할 때 override하는 것 처럼 모던cpp에서도 생김.
cs과 달리 끝에 적음
final또한 마지막으로 재정의한다는 뜻으로 밀봉하는 역할


7. rvalue reference



8. forwarding reference



9. lambda





10. smart pointer



