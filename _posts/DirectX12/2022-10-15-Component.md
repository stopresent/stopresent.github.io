---
title: "C++ Rookiss Part2 게임 수학과 DirectX12 : Component 복습"

categories:
  - DirectX12
tags:
  - DirectX12

author_profile: false

sidebar:
  nav: "docs"

date: 2022-10-15
last_modified_at: 2022-10-16
---

<br>

### 🚀 Component

1. GameObject 필터에 Component, GameObject, Transform, MeshRenderer, MonoBehaviour가 추가
2. Component 패턴으로 구조 바꾸기

Component 패턴이란 유니티의 구조랑 똑같다. 유니티에서 기본 gameObject를 만들면 Transform만 있고 아무것도 없는데 여기서 부품들을 추가하면 cube나 다른 여러가지로 만들어진다.

여기서 핵심은 부품들을 추가하면 원하는 오브젝트가 만들어진다는 것.

언리얼은 상속구조로 되어있는데 cube를 생성하더라도 static_charater이런식의 상속이 이미 되어있다.
상속이 되어있는 오브젝트라서 구조가 이미 어느정도 정해져있다.

**유니티 : 가벼움, 사용자가 사용하기 편함, 바닥부터 만들어야 됨, 컴포넌트 구조**
**언리얼 : 무거움, 사용할려면 많이 배워야 됨, 이미 구조가 짜여있음, 상속 구조**

**Component**

```cpp
#pragma once

enum class COMPONENT_TYPE : uint8
{
	TRANSFORM,
	MESH_RENDERER,
	// ...
	MONO_BEHAVIOUR,
	END,
};

enum
{
	FIXED_COMPONENT_COUNT = static_cast<uint8>(COMPONENT_TYPE::END) - 1
};

class GameObject;
class Transform;

class Component
{
public:
	Component(COMPONENT_TYPE type);
	virtual ~Component();

public:
	virtual void Awake() { }
	virtual void Start() { }
	virtual void Update() { }
	virtual void LateUpdate() { }

public:
	COMPONENT_TYPE GetType() { return _type; }
	bool IsValid() { return _gameObject.expired() == false; }

	shared_ptr<GameObject> GetGameObject();
	shared_ptr<Transform> GetTransform();

private:
	friend class GameObject;
	void SetGameObject(shared_ptr<GameObject> gameObject) { _gameObject = gameObject; }

protected:
	COMPONENT_TYPE _type;
	weak_ptr<GameObject> _gameObject;
};
```

Component 클래스는 최종 부모 클래스 역할이기 때문에 소멸자에 꼭꼭!! virtual을 붙여야한다.
이유는 cpp1장 참고.
`weak_ptr<GameObject> _gameObject;` 에서 shared_ptr이 아니라 `weak_ptr` 로 되어있는데 component가 gameobject를 가르키고 gameobject가 component를 가르키면서 순환구조가 생기기때문에 shared_ptr을 사용하지 않는다.
**shared_ptr은 레퍼런스 카운터를 가지고 있어서 순환구조가 완성되는 순간 레퍼러스 카운터가 내려가지 않는 문제가 발생한다. 순환구조에서 shared_ptr은 절대 사용하면 안된다**

enum class 에서 MonoBehaviour은 무조건 마지막 순서로 들고 있어야한다.
MonoBehaviour은 유니티에서 스크립트를 만들 때 기본적으로 상속받고 있는 클래스인데 이 친구는 특별하기 때문에 마지막에서 다른 행동을 취할 것 이므로 마지막에 넣는다.

```cpp
#include "pch.h"
#include "Component.h"
#include "GameObject.h"

Component::Component(COMPONENT_TYPE type) : _type(type)
{

}

Component::~Component()
{
}

shared_ptr<GameObject> Component::GetGameObject()
{
	return _gameObject.lock();
}

shared_ptr<Transform> Component::GetTransform()
{
	return _gameObject.lock()->GetTransform();
}
```

`_gameObject.lock();` 이게 무슨 함순지 모르겠는데 memory.h 에서 shared_ptr을 뱉어주는 함수인 것 같다. friend class 로 엮여있으니 레퍼런스 관리하는 듯??

**GameObject**

```cpp
#pragma once
#include "Component.h"

class Transform;
class MeshRenderer;
class MonoBehaviour;

class GameObject : public enable_shared_from_this<GameObject>
{
public:
	GameObject();
	virtual ~GameObject();

	void Init();

	void Awake();
	void Start();
	void Update();
	void LateUpdate();

	shared_ptr<Transform> GetTransform();

	void AddComponent(shared_ptr<Component> component);

private:
	array<shared_ptr<Component>, FIXED_COMPONENT_COUNT> _components;
	vector<shared_ptr<MonoBehaviour>> _scripts;
};
```

유니티 스타일로 Awake, Start, Update, LateUpdate가 존재하고 유니티는 기본 gameObject를 생성할 때 Transform을 들고 있으므로 똑같이 GetTransform을 만들어줬다. Component를 가르키고 있으므로 멤버변수에 component를 들고 있다.

`public enable_shared_from_this<GameObject>` 이걸 상속 받고 있는데 자기 자신의 스마트 포인터를 보낼 때 유용하다.

```cpp
#include "pch.h"
#include "GameObject.h"
#include "Transform.h"
#include "MeshRenderer.h"
#include "MonoBehaviour.h"

GameObject::GameObject()
{

}

GameObject::~GameObject()
{

}

void GameObject::Init()
{
	AddComponent(make_shared<Transform>());
}

void GameObject::Awake()
{
	for (shared_ptr<Component>& component : _components)
	{
		if (component)
			component->Awake();
	}

	for (shared_ptr<MonoBehaviour>& script : _scripts)
	{
		script->Awake();
	}
}

void GameObject::Start()
{
	for (shared_ptr<Component>& component : _components)
	{
		if (component)
			component->Start();
	}

	for (shared_ptr<MonoBehaviour>& script : _scripts)
	{
		script->Start();
	}
}

void GameObject::Update()
{
	for (shared_ptr<Component>& component : _components)
	{
		if (component)
			component->Update();
	}

	for (shared_ptr<MonoBehaviour>& script : _scripts)
	{
		script->Update();
	}
}

void GameObject::LateUpdate()
{
	for (shared_ptr<Component>& component : _components)
	{
		if (component)
			component->LateUpdate();
	}

	for (shared_ptr<MonoBehaviour>& script : _scripts)
	{
		script->LateUpdate();
	}
}

shared_ptr<Transform> GameObject::GetTransform()
{
	uint8 index = static_cast<uint8>(COMPONENT_TYPE::TRANSFORM);
	return static_pointer_cast<Transform>(_components[index]);
}

void GameObject::AddComponent(shared_ptr<Component> component)
{
	component->SetGameObject(shared_from_this());

	uint8 index = static_cast<uint8>(component->GetType());
	if (index < FIXED_COMPONENT_COUNT)
	{
		_components[index] = component;
	}
	else
	{
		_scripts.push_back(dynamic_pointer_cast<MonoBehaviour>(component));
	}
}
```

`public enable_shared_from_this<GameObject>` 를 상속 받고 있어서

`component->SetGameObject(shared_from_this());` 에서 `shared_from_this()` 로 자기 자신의 스마트 포인터를 넣을 수 있다.

**MeshRenderer**

```cpp
#pragma once
#include "Component.h"

class Mesh;
class Material;

class MeshRenderer : public Component
{
public:
	MeshRenderer();
	virtual ~MeshRenderer();

	void SetMesh(shared_ptr<Mesh> mesh) { _mesh = mesh; }
	void SetMaterial(shared_ptr<Material> material) { _material = material; }

	virtual void Update() override { Render(); }

	void Render();

private:
	shared_ptr<Mesh> _mesh;
	shared_ptr<Material> _material;
};
```

mesh와 material을 합해준 최종본. mesh, material 설정과 Render를 override(재정의)를 해주고 있다.

```cpp
#include "pch.h"
#include "MeshRenderer.h"
#include "Mesh.h"
#include "Material.h"

MeshRenderer::MeshRenderer() : Component(COMPONENT_TYPE::MESH_RENDERER)
{

}

MeshRenderer::~MeshRenderer()
{

}

void MeshRenderer::Render()
{
	//GetTransform()->Update();

	_material->Update();
	_mesh->Render();
}
```

**Transform**

```cpp
#pragma once
#include "Component.h"

struct TransformMatrix
{
	Vec4 offset;
};

class Transform : public Component
{
public:
	Transform();
	virtual ~Transform();

	// TODO : 온갖 Parent/Child 관계

private:
	// TODO : World 위치 관련

};
```

기존의 EnginePch.h에서 들고 있던 Transform 구조체를 여기로 옮겼고 나중에 게임수학에서 행렬로 쓰기때문에 이름이 변경

```cpp
#include "pch.h"
#include "Transform.h"

Transform::Transform() : Component(COMPONENT_TYPE::TRANSFORM)
{

}

Transform::~Transform()
{

}

// CONST_BUFFER(CONSTANT_BUFFER_TYPE::TRANSFORM)->PushData(&_transform, sizeof(_transform));
```

**MonoBehaviour**

```cpp
#pragma once
#include "Component.h"

class MonoBehaviour : public Component
{
public:
	MonoBehaviour();
	virtual ~MonoBehaviour();

public:

};
```

```cpp
#include "pch.h"
#include "MonoBehaviour.h"

MonoBehaviour::MonoBehaviour() : Component(COMPONENT_TYPE::MONO_BEHAVIOUR)
{

}

MonoBehaviour::~MonoBehaviour()
{

}
```

---

### 🚀 Component 패턴 사용

Transform, Material을 Mesh에서 관리를 안하므로 Mesh에서 삭제.

mesh.Render에서도 그와 관련된 모든 부분 삭제.

Game.cpp에서

```cpp
#include "GameObject.h"
#include "MeshRenderer.h"

shared_ptr<GameObject> gameObject = make_shared<GameObject>();
```

이렇게 gameObject라는 최종 깡통을 만든 뒤

```cpp
// 여기서부터 오늘 테스트
gameObject->Init(); // Transform

shared_ptr<MeshRenderer> meshRenderer = make_shared<MeshRenderer>();

{
	shared_ptr<Mesh> mesh = make_shared<Mesh>();
	mesh->Init(vec, indexVec);
	meshRenderer->SetMesh(mesh);
}

{
	shared_ptr<Shader> shader = make_shared<Shader>();
	shared_ptr<Texture> texture = make_shared<Texture>();
	shader->Init(L"..\\Resources\\Shader\\default.hlsli");
	texture->Init(L"..\\Resources\\Texture\\veigar.jpg");

	shared_ptr<Material> material = make_shared<Material>();
	material->SetShader(shader);
	material->SetFloat(0, 0.3f);
	material->SetFloat(1, 0.4f);
	material->SetFloat(2, 0.3f);
	material->SetTexture(0, texture);
	meshRenderer->SetMaterial(material);
}

gameObject->AddComponent(meshRenderer);

GEngine->GetCmdQueue()->WaitSync();
```

이런 패턴으로 사용이 된다.

결과화면은 이전과 같다.