---
title: "C++ Rookiss Part1 C++ 프로그래밍 입문 : smart pointer"

categories:
  - CPP
tags:
  - CPP

author_profile: false

sidebar:
  nav: "docs"

date: 2022-06-09
last_modified_at: 2022-06-09
---

<br>

## 🙇‍♀️smart pointer


<br>


### 🪐smart pointer


```cpp
#include <iostream>
using namespace std;

// smart pointer

class Knight
{
public:

	Knight() { cout << "Knight()" << endl; }
	~Knight() { cout << "~Knight()" << endl; }

	void Attack()
	{
		if (_target)
		{
			_target->_hp -= _damage;
			cout << "HP : " << _target->_hp << endl;
		}
	}

public:
	int _hp = 100;
	int _damage = 10;
	shared_ptr<Knight> _target = nullptr;
};

class RefCountBlock
{
public:
	int _refCount = 1;
};

template<typename T>
class SharedPtr
{
public:

	SharedPtr() {}
	SharedPtr(T* ptr) : _ptr(ptr)
	{
		if (_ptr != nullptr)
		{
			_block = new RefCountBlock();
			cout << "RefCount : " << _block->_refCount << endl;
		}
	}

	SharedPtr(const SharedPtr& sptr) : _ptr(sptr._ptr), _block(sptr._block)
	{
		if (_ptr != nullptr)
		{
			_block->_refCount++;
			cout << "RefCount : " << _block->_refCount << endl;
		}
	}

	void operator=(const SharedPtr& sptr)
	{
		_ptr = sptr._ptr;
		_block = sptr._block;

		if (_ptr != nullptr)
		{
			_block->_refCount++;
			cout << "RefCount : " << _block->_refCount << endl;
		}
	}

	~SharedPtr()
	{
		if (_ptr != nullptr)
		{
			_block->_refCount--;
			cout << "RefCount : " << _block->_refCount << endl;

			if (_block->_refCount == 0)
			{
				delete _ptr;
				delete _block;
				cout << "Delete Data" << endl;
			}
		}
	}

public:
	T* _ptr = nullptr;
	RefCountBlock* _block = nullptr;
};

int main()
{
	/*
	Knight* k1 = new Knight();
	Knight* k2 = new Knight();

	k1->_target = k2;

	delete k2;

	k1->Attack();

	삭제된 포인터를 가리키는 문제를 가지고 있음
	*/

	// 스마트 포인터 : 포인터를 알맞는 정책에 따라 관리하는 객체 (포인터를 래핑해서 사용)
	// shared_ptr, weak_ptr, unique_ptr

	shared_ptr<Knight> k1 = make_shared<Knight>();

	{
		shared_ptr<Knight> k2 = make_shared<Knight>();
		k1->_target = k2;
	}

	k1->Attack();

	return 0;
}
```