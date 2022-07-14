---
title: "C++ Rookiss Part2 게임 수학과 DirectX12 : Transformation"

categories:
  - DirectX12
tags:
  - DirectX12

author_profile: false

sidebar:
  nav: "docs"

date: 2022-07-11
last_modified_at: 2022-07-11
---

<br>


## 🙇‍♀️Transformation


<br>


### 🪐선형변환


* 선형변환
  - t(u + v) = t(u) + t(v)
  - t(ku) = kt(u)

t가 선형변환이면 t(au + bv +cw) = at(u) + bt(v) + ct(w)

<br>

u = (x,y,z) = xi + yj + zk = x(1,0,0) + y(0,1,0) + z(0,0,1)

이때 i, j, k를 **표준기저벡터**라고 함

<br>

t(u) = t(xi + yj + zk) = xt(i) + yt(j) + zt(k)

=> uA = [x,y,z] [<-t(i)->   <-t(j)->  <-t(k)->] = [x,y,z] [A11 A12 A13 <-> A21 A22 A23 <-> A31 A32 A33]

t(i) = (A11, A12, A13), t(j) = (A21, A22, A23), t(k) = (A31, A32, A33)

이때 행렬 A를 **선형변환 t의 행렬표현**이라고 한다.

<br>

### 🪐비례

비례변환 => S(x,y,z) = (SxX, SyY, SzZ)

비례변환은 선형변환이다.

