---
layout: single
title: "C# Rookiss Part3 유니티 엔진 : ResourceManager"
categories: Unity
tags: Unity
author_profile: false
sidebar:
  nav: "docs"
---

## 🙇‍♀️ResourceManager

* ResourceManager는 프리펩 생성 및 파괴등을 맡는 매니저이다
  * Load<>()를 사용해 path경로의 프리펩을 GameObject로 불러낸다
  * Instantiate()로 GameObject를 프로젝트내에 생성
  * Destroy()로 오브젝트를 파괴한다

### 🪐전체코드

```cs
public T Load<T>(string path) where T : Object
{
    return Resources.Load<T>(path);
}

public GameObject Instantiate(string path, Transform parent = null)
{
    GameObject original = Load<GameObject>($"Prefabs/{path}");
    if (original == null)
    {
        Debug.Log($"Failed to load Prefabs {path}");
        return null;
    }

    GameObject go = Object.Instantiate(original, parent);
    go.name = original.name;
    return go;
}

public void Destroy(GameObject go)
{
    if (go == null)
        return;

    Object.Destroy(go);
}
```
