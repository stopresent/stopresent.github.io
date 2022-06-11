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

pch.cpp, pch.h, Types.h는 기본적으로 만들어 주는 것

콘솔의 그림, 색 등등의 함수들을 관리하는 ConsoleHelper.cpp, ConsoleHelper.h

main함수에서 프레임 관리(while)

보드와 관련된 것들은 Board.cpp에 이동

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

struct Pos // 좌표 인자를 가지는 구조체
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

enum Dir // 방향 enum
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

enum class ConsoleColor // 색 담는 그릇 클래스, 사용시 ConsoleColor::RED처럼 사용
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
	static void SetCursorPosition(int32 x, int32 y); // 커서 좌표 설정
	static void SetCursorColor(ConsoleColor color); // 커서 색 설정
	static void ShowConsoleCursor(bool flag); // 콘솔 커서 보여주기
};
```

<br>

### 🪐ConsoleHelper.cpp

window API같은데 내가 어찌 알아 그냥 적어야지

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


<br>

### 🪐Board.h

```cpp
#pragma once
#include "ConsoleHelper.h"

enum // 최대 사이즈
{
	BOARD_MAX_SIZE = 100
};

enum class TileType // 타일의 타입을 enum class로 정의 함
{
	NONE = 0,
	EMPTY,
	WALL,
};

class Board
{
public:
	Board(); // 생성자
	~Board(); // 소멸자

	void			Init(int32 size, class Player* player); // 판 생성시 시작 함수, player를 포인터로 받아 진퉁 처리
	void			Render(); // 판 그리기, 프레임으로 관리 됨

	void			GenerateMap(); // 맵 생성
	TileType		GetTileType(Pos pos); // 좌표의 타일 타입을 가져오는 함수
	ConsoleColor	GetTileColor(Pos pos); // 좌표의 타일 색을 가져오는 함수

	Pos				GetEnterPos() { return Pos{ 1, 1 }; } // 시작 위치
	Pos				GetExitPos() { return Pos{ _size - 2, _size - 2 }; } // 도착 위치

private:
	TileType		_tile[BOARD_MAX_SIZE][BOARD_MAX_SIZE] = { }; // 타일을 최대 크기로 2차 배열로 가지고 있음
	int32			_size = 0; // 크기
	Player*			_player = nullptr; // 플레이어
};
```


<br>

### 🪐Board.cpp


```cpp
#include "pch.h"
#include "Board.h"
#include "ConsoleHelper.h"
#include "Player.h"

const char* TILE = "■"; // 기본 타일을 전역변수로 들고있음

Board::Board()
{

}

Board::~Board()
{
}

void Board::Init(int32 size, Player* player) // 시작 함수에 크기와 플레이어를 넣어주기
{
	_size = size;
	_player = player;

	GenerateMap(); // 맵 생성
}

void Board::Render() // 맵 그리기 함수
{
	ConsoleHelper::SetCursorPosition(0, 0); // 먼저 좌표 설정 (0, 0)
	ConsoleHelper::ShowConsoleCursor(false); // 콘솔 커서는 false


	for (int y = 0; y < 25; y++)
	{
		for (int x = 0; x < 25; x++)
		{
			ConsoleColor color = GetTileColor(Pos{ y, x }); // 타일의 색을 가져오고
			ConsoleHelper::SetCursorColor(color); // 색을 기준에 맞게 설정한다
			cout << TILE; // 타일을 출력
		}

		cout << endl;
	}
}

// Binary Tree 미로 생성 알고리즘
// - Maze for Programmers
void Board::GenerateMap()
{
	for (int32 y = 0; y < _size; y++)
	{
		for (int32 x = 0; x < _size; x++)
		{
			if (x % 2 == 0 || y % 2 == 0) // 격자 모양으로 벽을 만든다
				_tile[y][x] = TileType::WALL;
			else
				_tile[y][x] = TileType::EMPTY;
		}
	}

	// 랜덤으로 우측 혹은 아래로 길 뚫기
	for (int32 y = 0; y < _size; y++)
	{
		for (int32 x = 0; x < _size; x++)
		{
			if (x % 2 == 0 || y % 2 == 0) // 격자 모양 벽인 경우 continue
				continue;

			if (y == _size - 2 && x == _size - 2) // 맨 하단 우측 벽 뚫리는 거 막기
				continue;

			if (y == _size - 2) // 하단 벽 뚫리는 거 막기
			{
				_tile[y][x + 1] = TileType::EMPTY;
				continue;
			}

			if (x == _size - 2) // 우측 벽 뚫리는 거 막기
			{
				_tile[y + 1][x] = TileType::EMPTY;
				continue;
			}

			const int32 randValue = ::rand() % 2; // 랜덤 숫자(0~1) 생성
			if (randValue == 0) // 0이면 우측 뚫기
			{
				_tile[y][x + 1] = TileType::EMPTY;
			}
			else // 1이면 하단 뚫기
			{
				_tile[y + 1][x] = TileType::EMPTY;
			}
		}
	}
}

TileType Board::GetTileType(Pos pos) // 좌표의 타일 타입 가져오기
{
	if (pos.x < 0 || pos.x >= _size) // 예외 처리
		return TileType::NONE;

	if (pos.y < 0 || pos.y >= _size) // 예외 처리
		return TileType::NONE;

	return _tile[pos.y][pos.x]; 
}

ConsoleColor Board::GetTileColor(Pos pos) // 타일 색 가져오기
{
	if (_player && _player->GetPos() == pos) // 플레이어가 nullptr이 아니고 플레이어 좌표와 pos가 같으면 YELLOW
		return ConsoleColor::YELLOW;

	if (GetExitPos() == pos) // 출구는 BLUE
		return ConsoleColor::BLUE;

	TileType tileType = GetTileType(pos);

	switch (tileType) // 타일 타입에 따라 색 설정
	{
	case TileType::EMPTY:
		return ConsoleColor::GREEN;
	case TileType::WALL:
		return ConsoleColor::RED;
	}

	return ConsoleColor::WHITE; // 기본 값은 하얀색
}
```


<br>

### 🪐Player.h

```cpp
#pragma once

class Board; // Board 전방 선언

class Player
{
public:
	void		Init(Board* board); // 보드 진퉁 사용
	void		Update(uint64 deltaTick); // 프레임 관리를 통한 업데이트

	void		SetPos(Pos pos) { _pos = pos; } // 좌표 설정
	Pos			GetPos() { return _pos; } // 좌표 가져오기

private:
	Pos			_pos = {}; // pch.h에 있는 구조체 y, x 넣어야 됨
	int32		_dir = DIR_UP; // 기본 방향 값은 UP
	Board*		_board = nullptr; // 보드 멤버변수
};
```


<br>

### 🪐Player.cpp

이제 구현해야 될 차례

```cpp
#include "pch.h"
#include "Player.h"
#include "Board.h"

void Player::Init(Board* board)
{
	_pos = board->GetEnterPos(); // 시작 좌표에 플레이어 넣어주기
	_board = board;
}

void Player::Update(uint64 deltaTick)
{

}
```


<br>

### 🪐Maze.cpp

```cpp
#include <iostream>
#include "pch.h"
#include "ConsoleHelper.h"
#include "Board.h"
#include "Player.h"

Board board; // 보드 전역 변수
Player player; // 플레이어 전역 변수

int main()
{
	::srand(static_cast<unsigned>(time(nullptr))); // 랜덤 시드 생성
	board.Init(25, &player); // 시작은 25크기에 플레이어 설정
	player.Init(&board); // 플레이어 시작, 진투 보드 삽입

	uint64 lastTick = 0;
	while (true)
	{
#pragma region 프레임 관리
		const uint64 currenTick = ::GetTickCount64();
		const uint64 deltaTick = currenTick - lastTick;
		lastTick = currenTick;
#pragma endregion
		// 입력

		// 로직
		player.Update(deltaTick);

		// 렌더링
		board.Render();
		
	}
}
```