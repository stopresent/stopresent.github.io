---
title: "C++ Rookiss Part1 C++ 프로그래밍 입문 : Maze Prepare"

categories:
  - Algorithm
tags:
  - Algorithm

author_profile: false

sidebar:
  nav: "docs"

date: 2022-06-10
last_modified_at: 2022-06-10
---

<br>

## 🙇‍♀️Maze Prepare


<br>


### 🪐프레임 관리

```cpp
uint64 lastTick = 0;
	while (true)
	{
#pragma region 프레임 관리
		const uint64 currenTick = ::GetTickCount64();
		const uint64 deltaTick = currenTick - lastTick;
		lastTick = currenTick;
#pragma endregion
    }
```
### 🪐프레임 관리