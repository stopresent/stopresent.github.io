---
layout: single
title: "Player Animation"
categories: MMORPG
tags: MMORPG
author_profile: false
sidebar:
  nav: "docs"
---


## 🙇‍♀️Player Animation



### 🪐Player Animation


플레이어에게 Animator 컴포넌트 넣어주고, AnimatorController생성

이동에 맞게 플레이어에게 스프라이트를 넣어주기

**생성자로 애니메이터 효과 넣어주기**
```cs
MoveDir _dir = MoveDir.None;
public MoveDir Dir
{
    get { return _dir; }
    set
    {
        if (_dir == value)
            return;

        switch (value)
        {
            case MoveDir.Up:
                _animator.Play("WALK_BACK");
                transform.localScale = new Vector3(1.0f, 1.0f, 1.0f);
                break;
            case MoveDir.Down:
                _animator.Play("WALK_FRONT");
                transform.localScale = new Vector3(1.0f, 1.0f, 1.0f);
                break;
            case MoveDir.Left:
                _animator.Play("WALK_RIGHT");
                transform.localScale = new Vector3(-1.0f, 1.0f, 1.0f);
                break;
            case MoveDir.Right:
                _animator.Play("WALK_RIGHT");
                transform.localScale = new Vector3(1.0f, 1.0f, 1.0f);
                break;
            case MoveDir.None:
                if (_dir == MoveDir.Up)
                {
                    _animator.Play("IDLE_BACK");
                    transform.localScale = new Vector3(1.0f, 1.0f, 1.0f);
                }
                else if (_dir == MoveDir.Down)
                {
                    _animator.Play("IDLE_FRONT");
                    transform.localScale = new Vector3(1.0f, 1.0f, 1.0f);
                }
                else if (_dir == MoveDir.Left)
                {
                    _animator.Play("IDLE_RIGHT");
                    transform.localScale = new Vector3(-1.0f, 1.0f, 1.0f);
                }
                else
                {
                    _animator.Play("IDLE_RIGHT");
                    transform.localScale = new Vector3(1.0f, 1.0f, 1.0f);
                }
                break;
        }

        _dir = value;
    }
}
```
여기서 `_dir = value;`이 부분 뺴지말자;;

그리고 생성자를 만들어 줬으니까 `_dir`은 `Dir`로 전부 바꾸기

