---
layout: single
title: "InputManager"
categories: Unity
tags: Unity
author_profile: false
sidebar:
  nav: "docs"
---

## 🙇‍♀️InputManager

  * Action을 사용하여 구독을 신청받는다
  * 구독을 한 것들에게는 이벤트를 날려준다

### 🪐전체코드

```cs
public Action KeyAction = null;

void OnUpdate()
{
    if (Input.anyKey == false)
        return;

    if (KeyAction != null)
        KeyAction.Invoke();
}
```
