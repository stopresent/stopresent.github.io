---
layout: single
title: "C# Rookiss Part3 유니티 엔진 : mouseMove"
categories: Unity
tags: Unity
author_profile: false
sidebar:
  nav: "docs"
---

## 🙇‍♀️mouseMove

* 마우스 클릭을 레이로 받기
* 클릭 된 좌표구하기
* 방향벡터 구하기
* 이동 및 회전


### 🪐InputManager

```cs
public class InputManager
{
    public Action KeyAction = null;
    public Action<Define.MouseEvent> MouseAction = null;

    bool _pressed = false;

    public void OnUpdate()
    {
        if (Input.anyKey && KeyAction != null) // 키씹힙을 방지하기 위해 바꿈
            KeyAction.Invoke();

        if (MouseAction != null)
        {
            if (Input.GetMouseButton(0))
            {
                MouseAction.Invoke(Define.MouseEvent.Press);
                _pressed = true;
            }
            else
            {
                if (_pressed)
                    MouseAction.Invoke(Define.MouseEvent.Click);
                _pressed = false;
            }
        }
    }
}
```

* 새로운 구독 `public Action<Define.MouseEvent> MouseAction = null;`가 추가
* bool값으로 마우스를 눌렀는지 판단
  * 누르다가 때면 Click으로 반환
  * 누르는 중이면 press로 반환

### 🪐OnMouseClicked

```cs
void OnMouseClicked(Define.MouseEvent evt) //InuptManager에서 public Action<Define.MouseEvent> MouseAction = null;로 정의해서 Define.MouseEvent evt인자가 필요함
{
    if (evt != Define.MouseEvent.Click)
        return;

    Ray ray = Camera.main.ScreenPointToRay(Input.mousePosition);
    Debug.DrawRay(Camera.main.transform.position, ray.direction * 100.0f, Color.red, 1.0f);
    RaycastHit hit;
    int mask = (1 << 7); // Ground mask

    if (Physics.Raycast(ray, out hit, 100.0f, mask))
    {
        _destPos = hit.point; // 목저직 좌표 구하기
        _moveToDest = true;
        //Debug.Log($"Raycast Camera @ {hit.transform.gameObject.name}");
    }
}
```

* 쿨릭된 좌표구하기
* _moveToDest를 켜서 움직임 시작알리기


### 🪐이동구현

```cs
Vector3 dir = _destPos - transform.position; // 방향 벡터구하기
if (_moveToDest)
{
    if (dir.magnitude > 0.01f) //목적지까지 도착하면 _moveToDest종료
    {
        _moveToDest = false;
    }
}
else
{
    float moveDestPos = Mathf.Clamp(_speed * Time.deltaTime, 0, dir.magnitude); //

    transform.position += dir.normalized * moveDestPos;
    //transform.position += dir.normalized * _speed * Time.deltaTime;
    transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.LookRotation(dir), 10 * Time.deltaTime);
    transform.LookAt(_destPos);
}
```
* 방향벡터 구하기
* **Mathf.Clamp**로 부들거림 방지하기!
  * `transform.position += dir.normalized * _speed * Time.deltaTime;`로 구현하면 목적지에 도착하면 떨림
  * `float moveDestPos = Mathf.Clamp(_speed * Time.deltaTime, 0, dir.magnitude);`로 moveDestPos을 구한후
  * `transform.position += dir.normalized * moveDestPos;`을 해야 떨림이 사라짐!!
* 목적지 방향으로 바라보기
  * `transform.LookAt(_destPos);` 목적지로 바라보기
  * `transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.LookRotation(dir), 10 * Time.deltaTime);`로 목적지로 바라볼 때 부드럽게 
