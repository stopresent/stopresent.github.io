---
layout: single
title: "Controller 정리"
categories: MMORPG
tags: MMORPG
author_profile: false
sidebar:
  nav: "docs"
---


## 🙇‍♀️Controller 정리


Monster, Player의 부모 스크립트를 생성해주기


### 🪐Controller 정리


MonsterController, CreatureController 생성


PlayerController의 내용을 모두 CreatureController에 삽입
다른 애들도 쓸 수 있는 것들 ( `_cellPos`, `_isMoving`, `_animator`)는 protected로 바꿔주기

```cs
protected virtual void Init()
{
  _animator = GetComponent<Animator>();
  Vector3 pos = Managers.Map.CurrentGrid.CellToWorld(_cellPos) + new Vector3(0.5f, 0.5f);
  transform.position = pos;
}

protected virtual void UpdateController()
{
  UpdatePosition();
  UpdateIsMoving();
}
```

카메라 부분, 키보드 입력은 삭제

플레이어 컨트롤러에서 크리처컨트롤러에서 지우지않은것들은 삭제

```cs
protected override void Init()
{
  base.Init();
}

protected override void UpdateController()
{
  GetDirInput();
  base.UpdateController();
}
```

몬스터컨트롤러는 플레이어 컨트롤러에서 카메라만 삭제하고 GetDirInput은 주석처리


Define에 State추가
```cs
public enum CreatureState
{
  Idle,
  Moving,
  Skill,
  Dead,
}
```

CreatureController에 넣어주기
```cs
CreatureState _state = CreatureState.Idle;
public CreatureState State
{
 get { return _state; }
 set 
 {
    if ( _state == value)
      return;
    
    _state = value;
  }
}
```

`_isMoving`을 삭제하고 state 상태로 바꿔주기


애니메이션 관리

```cs
protected virtual void UpdateAnimation()
{
    if (_state == CreatureState.Idle)
    {
        switch (_lastDir)
        {
            case MoveDir.Up:
                _animator.Play("IDLE_BACK");
                _sprite.flipX = false;
                break;
            case MoveDir.Down:
                _animator.Play("IDLE_FRONT");
                _sprite.flipX = false;
                break;
            case MoveDir.Left:
                _animator.Play("IDLE_RIGHT");
                _sprite.flipX = true;
                break;
            case MoveDir.Right:
                _animator.Play("IDLE_RIGHT");
                _sprite.flipX = false;
                break;
        }
    }
    else if (_state == CreatureState.Moving)
    {
        switch (_dir)
        {
            case MoveDir.Up:
                _animator.Play("WALK_BACK");
                _sprite.flipX = false;
                break;
            case MoveDir.Down:
                _animator.Play("WALK_FRONT");
                _sprite.flipX = false;
                break;
            case MoveDir.Left:
                _animator.Play("WALK_RIGHT");
                _sprite.flipX = true;
                break;
            case MoveDir.Right:
                _animator.Play("WALK_RIGHT");
                _sprite.flipX = false;
                break;
        }
    }
    else if (_state == CreatureState.Skill)
    {
        //TODO
    }
    else
    {

    }
}
```





