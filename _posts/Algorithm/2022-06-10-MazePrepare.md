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

`while`문 속에서 현재 시간 카운트 함수인 `GetTickCount64()`를 이용해서 `lastTick`을 갱신한다

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

<br>

### 🪐Types.h

`__int8`치럼 `__`을 쓰는게 귀찮으니까 `using`키워드를 이용하여 간편하게 사용하기

```cpp
#pragma once


using int8		= __int8;
using int16		= __int16;
using int32		= __int32;
using int64		= __int64;
using uint8		= unsigned __int8;
using uint16	= unsigned __int16;
using uint32	= unsigned __int32;
using uint64	= unsigned __int64;
```

<br>

### 🪐pch.h

precompiled header의 약자로 `#include`들의 총 집합

```cpp
#pragma once

#include "Types.h"
#include <Windows.h>
#include <iostream>
#include <vector>
using namespace std;

struct Pos
{
	bool operator==(Pos& other)
	{
		return y == other.y && x == other.x;
	}

	bool operator!=(Pos& other)
	{
		return !(*this == other);
	}

	Pos operator+(Pos& other)
	{
		Pos ret;
		ret.y = y + other.y;
		ret.x = x + other.x;
		return ret;
	}

	Pos& operator+=(Pos& other)
	{
		y += other.y;
		x += other.x;
		return *this;
	}

	int32 y = 0;
	int32 x = 0;
};

enum Dir
{
	DIR_UP = 0,
	DIR_LEFT = 1,
	DIR_DOWN = 2,
	DIR_RIGHT = 3,

	DIR_COUNT = 4,
};

```

<br>

### 🪐ConsoleHelper.h

```cpp
#pragma once
#include <Windows.h>
#include "Types.h"

enum class ConsoleColor
{
	BLACK = 0,
	RED = FOREGROUND_RED,
	GREEN = FOREGROUND_GREEN,
	BLUE = FOREGROUND_BLUE,
	YELLOW = RED | GREEN,
	WHITE = RED | GREEN | BLUE,
};

class ConsoleHelper
{
public:
	static void SetCursorPosition(int32 x, int32 y);
	static void SetCursorColor(ConsoleColor color);
	static void ShowConsoleCursor(bool flag);
};
```

<br>

### 🪐ConsoleHelper.cpp

```cpp
#include "pch.h"
#include "ConsoleHelper.h"

void ConsoleHelper::SetCursorPosition(int32 x, int32 y)
{
	HANDLE output = ::GetStdHandle(STD_OUTPUT_HANDLE);
	COORD pos = { static_cast<SHORT>(x),static_cast<SHORT>(y) };
	::SetConsoleCursorPosition(output, pos);
}

void ConsoleHelper::SetCursorColor(ConsoleColor color)
{
	HANDLE output = ::GetStdHandle(STD_OUTPUT_HANDLE);
	::SetConsoleTextAttribute(output, static_cast<int16>(color));
}

void ConsoleHelper::ShowConsoleCursor(bool flag)
{
	HANDLE output = ::GetStdHandle(STD_OUTPUT_HANDLE);
	CONSOLE_CURSOR_INFO cursorInfo;
	::GetConsoleCursorInfo(output, &cursorInfo);
	cursorInfo.bVisible = flag;
	::SetConsoleCursorInfo(output, &cursorInfo);
}
```