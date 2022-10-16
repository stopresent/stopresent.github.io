---
title: "C++ Rookiss Part2 게임 수학과 DirectX12 : Input Timer 복습"

categories:
  - DirectX12
tags:
  - DirectX12

author_profile: false

sidebar:
  nav: "docs"

date: 2022-10-12
last_modified_at: 2022-10-16
---

<br>

### 🚀 Input

```cpp
#pragma once

enum class KEY_TYPE
{
	UP = VK_UP,
	DOWN = VK_DOWN,
	LEFT = VK_LEFT,
	RIGHT = VK_RIGHT,

	W = 'W',
	S = 'S',
	A = 'A',
	D = 'D',
};

enum class KEY_STATE
{
	NONE,
	PRESS,
	DOWN,
	UP,
	END,
};

enum
{
	KEY_TYPE_COUNT = static_cast<int32>(UINT8_MAX + 1),
	KEY_STATE_COUNT = static_cast<int32>(KEY_STATE::END),
};

class Input
{
public:
	void Init(HWND hwnd);
	void update();

	bool GetButton(KEY_TYPE key) { return GetState(key) == KEY_STATE::DOWN; }
	bool GetButtonDown(KEY_TYPE key) { return GetState(key) == KEY_STATE::PRESS; }
	bool GetButtonUp(KEY_TYPE key) { return GetState(key) == KEY_STATE::UP; }

private:
	inline KEY_STATE GetState(KEY_TYPE key) { return _states[static_cast<uint8>(key)]; }

private:
	HWND				_hwnd;
	vector<KEY_STATE>	_states;
};
```

유니티와 비슷한 코드로 작성 됨.

```cpp
#include "pch.h"
#include "Input.h"
#include "Engine.h"

void Input::Init(HWND hwnd)
{
	_hwnd = hwnd;
	_states.resize(KEY_TYPE_COUNT, KEY_STATE::NONE);
}

void Input::update()
{
	HWND hwnd = ::GetActiveWindow();
	if (_hwnd != hwnd)
	{
		for (uint32 key = 0; key < KEY_TYPE_COUNT; key++)
			_states[key] = KEY_STATE::NONE;

		return;
	}

	for (uint32 key = 0; key < KEY_TYPE_COUNT; key++)
	{
		// 키가 눌려 있으면 true
		if (::GetAsyncKeyState(key) & 0x8000)
		{
			KEY_STATE& state = _states[key];

			// 이전 프레임에 키를 누른 상태라면 PRESS
			if (state == KEY_STATE::PRESS || state == KEY_STATE::DOWN)
				state = KEY_STATE::PRESS;
			else
				state = KEY_STATE::DOWN;
		}
		else
		{
			KEY_STATE& state = _states[key];

			// 이전 프레임에 키를 누른 상태라면 UP
			if (state == KEY_STATE::PRESS || state == KEY_STATE::DOWN)
				state = KEY_STATE::UP;
			else
				state = KEY_STATE::NONE;
		}
	}
}
```

`if (::GetAsyncKeyState(key) & 0x8000)` 부분은 다른 API니까 무시해도 됨

Engine에서 Update함수를 만들고 `_input->Update();` 를 삽입.

game→Update 부붙에서

```cpp
{
		static Transform t = {};

		if (INPUT->GetButtonDown(KEY_TYPE::W))
			t.offset.y += 1.f * 0.001;
		if (INPUT->GetButtonDown(KEY_TYPE::S))
			t.offset.y -= 1.f * 0.001;
		if (INPUT->GetButtonDown(KEY_TYPE::A))
			t.offset.x -= 1.f * 0.001;
		if (INPUT->GetButtonDown(KEY_TYPE::D))
			t.offset.x += 1.f * 0.001;

		mesh->SetTransform(t);

		mesh->SetTexture(texture);

		mesh->Render();
	}
```

로 변경.

W, A, S, D에 입력 값에 따라서 t.offset이 변경 된다.

유니티에서는 뒤에 deltaTime이 붙는데 이를 만들기 위해서 Timer를 제작한다.

---

### 🚀 Timer

```cpp
#pragma once

class Timer
{
public:
	void Init();
	void Update();

	uint32 GetFps() { return _fps; }
	float GetDeltaTime() { return _deltaTime; }

private:
	uint64 _frequency = 0;
	uint64 _prevCount = 0;
	float _deltaTime = 0.f;

private:
	uint32	_frameCount = 0;
	float	_frameTime = 0;
	uint32	_fps = 0;
};
```

```cpp
#include "pch.h"
#include "Timer.h"

void Timer::Init()
{
	::QueryPerformanceFrequency(reinterpret_cast<LARGE_INTEGER*>(&_frequency));
	::QueryPerformanceCounter(reinterpret_cast<LARGE_INTEGER*>(&_prevCount));
}

void Timer::Update()
{
	uint64 currentCount;
	::QueryPerformanceCounter(reinterpret_cast<LARGE_INTEGER*>(&currentCount));

	_deltaTime = (currentCount - _prevCount) / static_cast<float>(_frequency);
	_prevCount = currentCount;

	_frameCount++;
	_frameTime += _deltaTime;

	if (_frameTime > 1.f)
	{
		_fps = static_cast<uint32>(_frameCount / _frameTime);

		_frameTime = 0.f;
		_frameCount = 0;
	}
}
```

`QueryPerformanceFrequency` 는 `GetTickCount` 보다 더 정밀한 함수

`_deltaTime = (currentCount - _prevCount) / static_cast<float>(_frequency);` 은 틱에서 초로 바꾸는 것.

```cpp
void Engine::ShowFps()
{
	uint32 fps = _timer->GetFps();

	WCHAR text[100] = L"";
	::wsprintf(text, L"FPS : %d", fps);

	::SetWindowText(_window.hwnd, text);
}
```

윈도우 창에 fps를 띄우는 함수

---