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
  - $t(u + v) = t(u) + t(v)$
  - $t(ku) = kt(u)$

t가 선형변환이면 $t(au + bv +cw) = at(u) + bt(v) + ct(w)$

<br>

$u = (x,y,z) = xi + yj + zk = x(1,0,0) + y(0,1,0) + z(0,0,1)$

이때 i, j, k를 **표준기저벡터**라고 함

<br>

$$t(u) = t(xi + yj + zk) = xt(i) + yt(j) + zt(k)$$

=> $uA$ = $\begin{bmatrix}x&y&z\\ \end{bmatrix}$ $\begin{bmatrix}t(i)\\t(j)\\t(k)\\ \end{bmatrix}$  = $\begin{bmatrix}x&y&z\\ \end{bmatrix}$ $\begin{bmatrix}A11&A12&A13\\A21&A22&A23\\A31&A32&A33\\ \end{bmatrix}$

$t(i) = (A11, A12, A13), t(j) = (A21, A22, A23), t(k) = (A31, A32, A33)$

이때 행렬 A를 **선형변환 t의 행렬표현**이라고 한다.

<br>

### 🪐비례

$$비례변환 => S(x,y,z) = (S_{x}X, S_{y}Y, S_{z}Z)$$

비례변환은 선형변환이다.

S = $\begin{bmatrix}Sx&0&0\\0&Sy&0\\0&0&Sz\\ \end{bmatrix}$ 

이때 행렬 S를 **비례행렬**이라고 한다.

* 예시
  - 최솟점 $(-4, -4, 0)$, 최댓점 $(4, 4, 0)$
  - x축으로 0.5단위 y축으로 2단위 비례
    - => $비례행렬 = \begin{bmatrix}0.5&0&0\\0&2&0\\0&0&0\\ \end{bmatrix}$
    - 최솟점과 최댓점을 비례행렬에 곱한다
    - => $\begin{bmatrix}-2&-8&0\\ \end{bmatrix}$ $\begin{bmatrix}2&8&0\\ \end{bmatrix}$

<br>

### 🪐회전

벡터 $v$를 축 $n$에 대해 회전하는 변환. $||n|| = 1$이라고 가정

![벡터 n에 대한 기하구조의 회전](https://user-images.githubusercontent.com/86364202/178959623-795ec309-09f9-4f66-9592-d47b49331e92.jpg)

수지 부분은 $v_{\bot} = perp_{n}(v) = v - proj_{n}(v)$로 주어진다

$n$이 단위벡터이므로 $proj_{n}(v) = (n \cdot v)n$이다.

$proj_{n}(v)$는 회전에 대해 불변이므로 수직인 부분을 회전하는 방법만 알아내면 된다.

$R_{n}(v) = proj_{n}(v) + R_{n}(v_{\bot})$이므로 $R_{n}(v_{\bot})$만 구하면 된다.

$R_{n}(v_{\bot})$을 구하기위해, 회전 평면에 하나의 2차원 좌표계를 설정.
$v_{\bot}$를 두 기준 벡터 중 하나로 사용. 다른 하나는 $v_{\bot}$와 $n$에 수직인 벡터이어야 한다. 그러한 벡터는 외적 $n \times v$로 구하면 된다. 삼각함수 공식들에 의해

$\vert\vert n \times v \vert\vert = ||n|| ||v|| sin\alpha = \vert\vert v \vert\vert sin\alpha = \vert\vert v_{\bot} \vert\vert$이다.

두 기준 벡터는 크기가 같기 때문에 $R_{n}(v_{\bot}) = cos\theta v_{\bot} + sin\theta(n \times v)$ 이다.

따라서

$R_{n}(v) \\ = proj_{n}(v) + R_{n}(v) \\ = (n \cdot v)n + cos\theta v_{\bot} + sin\theta(n \times v) \\ = (n \cdot v)n + cos\theta(v - (n \cdot v)n) + sin\theta(n \times v) \\ = cos\theta v + (1 - cos\theta)(n \cdot v)n + sin\theta(n \times v)$

$R_{n}$을 표준기저벡터에 적용하고, 그래서 나온 벡터들을 행으로 삼아서 하나의 행렬을 만들면 구할수 있다. 최종 결과인 **회전행렬**은 다음과 같다.



