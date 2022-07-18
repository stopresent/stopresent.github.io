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

$\vert\vert n \times v \vert\vert = ||n|| \, ||v|| \, sin\alpha = \vert\vert v \vert\vert \, sin\alpha = \vert\vert v_{\bot} \vert\vert$이다.

두 기준 벡터는 크기가 같기 때문에 $R_{n}(v_{\bot}) = cos\theta v_{\bot} + sin\theta(n \times v)$ 이다.

따라서

$R_{n}(v) \\ = proj_{n}(v) + R_{n}(v) \\ = (n \cdot v)n + cos\theta v_{\bot} + sin\theta(n \times v) \\ = (n \cdot v)n + cos\theta(v - (n \cdot v)n) + sin\theta(n \times v) \\ = cos\theta v + (1 - cos\theta)(n \cdot v)n + sin\theta(n \times v)$

$R_{n}$을 표준기저벡터에 적용하고, 그래서 나온 벡터들을 행으로 삼아서 하나의 행렬을 만들면 구할수 있다. 최종 결과인 **회전행렬**은 다음과 같다.

$R_{n} = \begin{bmatrix}c + (1 - c)x^2&(1 - c)xy + sz&(1 - c)xz - sy\\ (1 - c)xy - sz&c + (1 - c)y^2&(1 - c)yz + sx \\ (1 - c)xz + sy&(1 - c)yz - sz&c + (1 - c)z^2 \end{bmatrix}$ 

이떄 $c = cos\theta, s = sin\theta$이다.

회전행렬은 행렬의 각 행벡터는 단위 길이이고 행벡터들은 서로 직교이다. 따라서 행벡터들은 **정규직교**이다. 직교행렬에는 그 역행렬이 자신의 전치행렬과 같다.

$R_{n}^{-1} = R_{n}^T = \begin{bmatrix}c + (1 - c)x^2&(1 - c)xy - sz&(1 - c)xz + sy\\ (1 - c)xy + sz&c + (1 - c)y^2&(1 - c)yz - sz \\ (1 - c)xz - sy&(1 - c)yz + sx&c + (1 - c)z^2 \end{bmatrix}$ 


<br>

### 🪐아핀변환

1. 벡터를 나타내는 동차좌표는 $(x, y, z, 0)$
2. 점을 나타내는 동차좌표는 $(x, y, z, 1)$

아핀변환은 선형변환에 이동벡터 b를 더한 것이다. => $\alpha (u) = \tau (u) + b$

$I(u) = u$를 **항등변환**이라고 부른다.

이동변환은 선형변환 부분이 하나의 단위행렬인 아핀변환이라고 정의 할 수 있다.

$이동행렬 = \begin{bmatrix}1&0&0&0\\ 0&1&0&0 \\ 0&0&1&0 \\ b_{x}&b_{y}&b_{z}&1\end{bmatrix}$ 


<br>

### 🪐좌표 변경 변환


벡터 : $p_{A} = (x, y, z)라고 할 때, p_{B} = xu_{B} + yv_{B} + zw_{B}$

점 : $p_{A} = (x, y, z)라고 할 때, p_{B} = xu_{B} + yv_{B} + zw_{B} + Q_{B}$

좌표 변경 행렬 (좌표계 변경 행렬) : $\begin{bmatrix}u_{x}&u_{y}&u_{z}&0\\ v_{x}&v_{y}&v_{z}&0 \\ w_{x}&w_{y}&w_{z}&0 \\ Q_{x}&Q_{y}&Q_{z}&1\end{bmatrix}$ 


역행렬과 좌표 변경 행렬

$
p_{B} = p_{A}M \\
p_{B}M^{-1} = p_{A}MM^{-1} \\
p_{B}M^{-1} = p_{A}I \\
p_{B}M^{-1} = p_{A}
$


<br>

### 🪐요약

1. 기본 변환 행렬들, 즉 비례, 회전, 이동행렬은 다음과 같이 주어진다.

$S = \begin{bmatrix}S_{x}&0&0&0\\0&S_{y}&0&0\\0&0&S_{z}&0\\ 0&0&0&1 \end{bmatrix}$ 

$T = \begin{bmatrix}1&0&0&0\\0&1&0&0\\0&0&1&0\\ b_{x}&b_{y}&b_{z}&1 \end{bmatrix}$ 

$R_{n} = \begin{bmatrix}c + (1 - c)x^2&(1 - c)xy + sz&(1 - c)xz - sy & 0\\ 
(1 - c)xy - sz&c + (1 - c)y^2&(1 - c)yz + sx & 0 \\ 
(1 - c)xz + sy&(1 - c)yz - sz&c + (1 - c)z^2 & 0 \\
0&0&0&1
\end{bmatrix}$ 

2. 변환은 $4 \times 4$ 행렬로 나타내고 점과 벡터는 $1 \times 4$ 동차좌표로 나타낸다. 점을 나타낼때에는 넷째 성분을 1로, 즉 $w = 1$로 설정하고, 벡터를 나타낼 때에는 $w = 0$으로 설정한다. 이렇게 하면 점에 대해서는 이동변환이 적용되지만 벡터에는 적용되지 않는다.

3. 모든 행벡터가 단위 길이이고 서로 직교인 행렬을 직교행렬이라고 부른다. 직교행렬은 그 역이 전치와 같다는 특별한 성질을 가지고 있다. 따라서 역행렬을 쉽고 효율적으로 계산할 수 있다. 모든 회전행렬은 직교행렬이다.

4. 행렬 곱셈의 결합법칙 덕분에, 여러 개의 변환 행렬을 합성해서 그 행렬들을 차례로 적용했을 때와 같은 결과를 내는 하나의 변환 행렬을 만들 수 있다.

5. $Q_{B}$ 와 $u_{B}, v_{B}, w_{B}$가 각각 좌표계 $A$의 원점과 $x, y, z$ 축을 좌표계 $B$를 기준으로 서술한 좌표들이라고 하자. 어떤 벡터 또는 점 $P$의 좌표계 $A$ 기준 좌표가 $P_{A} = (x, y, z)$라고 할 때, 그 벡터 또는 점의 $B$ 기준 좌표는 다음과 같다.

(a) 벡터(방향과 크기)의 경우 $p_{B} = (x^`, y^`, z^`) = xu_{B} + yv_{B} + zw_{B}$

(b) 점(위치)의 경우 $p_{B} = (x^`, y^`, z^`) = Q_{B} + xu_{B} + yv_{B} + zw_{B}$

이러한 좌표 변경 변환들을 동차좌표를 이용해서 행렬들로 표현하는 것이 가능하다.

6. 세 좌표계 $F, G, H$가 있다고 하자. 그리고 $A$가 $F$에서 $G$로의 좌표 변경 행렬이고 $B$가 $G$에서 $H$로의 좌표 변경 행렬이라고 하자. 이때 행렬 곱 $C = AB$를  $F$에서 직접 $H$로 가는 좌표계 변경 행렬이라고 생각할 수 있다. 즉, 행렬 대 행렬 곱셈은 $A$와 $B$의 효과를 하나로 합친 행렬을 만들어 낸다. 따라서 $P_{F}(AB) = P_{H}$가 성립한다.

7. 행렬 $M$이 좌표계 $A$의 좌표들을 좌표계 $B$의 좌표들로 사상하는 행렬이라고 할 때, $M^{-1}$은 좌표계 $B$의 좌표들을 $A$의 좌표들로 사상하는 행렬이다.

8. 능동 변환들을 좌표 변경 변환으로 해석할 수 있으며, 그 역도 마찬가지이다. 여러 좌표계를 다루면서 물체 자체는 변경하지 않고 좌표계만 변환함으로써 좌표 표현이 바뀌게 하는 것이 더 직관적인 상황이 있는가 하면, 하나의 좌표계로 고정하고 그 좌표계 안에서 물체 자체를 변환하는 것이 더 직관적이 상황도 있다.