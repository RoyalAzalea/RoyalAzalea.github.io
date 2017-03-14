---
layout: post
title: "Kalman Filter Study _ 9장. 총정리"
date: 2017-03-13
categories: [kalman_filter, matlab]
---

* 칼만 필터는 5장 그림처럼 여러 단계에 걸쳐 복잡한 수식을 계산해야 추정값을 얻을 수
있는 컴퓨터 알고리즘이다. 한 개의 다항식으로 추정값을 계산해내는 1차 저주파 통과 필터와는
계산 과정이 많이 다르다.  

* 칼만 필터는 아래와 같은 수식으로 추정값을 계산한다. 그런데 이 계산식을 풀어 써보면
1차 저주파 필터와 비슷한 형태이다. 즉 칼만 필터는 예측값($\hat{x}_k^{-}$)과 현재
측정값($z_k$)에 적절한 가중치를 주고 더해서 추정값을 계산한다는 점에서 1차 저주파 필터와
비슷하다.  

$$
\begin{align}
\hat{x}_k &= \hat{x}_k^{-} + K_k({z_k - H\hat{x}_k^{-}}) \\\\
&= \hat{x}_k^{-} + K_kz_k - K_kH\hat{x}_k^{-} \\\\
&= (I - K_kH)\hat{x}_k^{-} + K_kz_k
\end{align}
$$  

* 그런데 칼만 필터에서는 추정값을 계산하는 가중치($K_k$)가 상수가 아니고, 매번 바뀌는
값이다. 즉 동일한 가중치를 사용하지 않고 공식에 따라 매번 새로 계산한다. 바로 이 점이
칼만 필터가 1차 저주파 필터와 크게 다른 점이다.  

$$
K_k = P_k^{-}H^{T}(HP_k^{-}H^{T} + R)^{-1}
$$  

* 오차 공분산($P_k$)은 추정값의 오차를 나타내는 척도로 사용된다. $P_k$가 크면 추정 오차도
크고, $P_k$가 작으면 추정 오차도 작다.  

$$
P_k = P_K^{-} - K_kHP_k^{-}
$$  

* $x_k$에 대한 칼만 필터의 추정값($\hat{x}_k$)과 오차 공분산($P_k$) 사이에는 다음과
같은 관계가 성립한다.  

$$
x_k \sim N(\hat{x}_k, P_k)
$$

* 칼만 필터 알고리즘에는 예측 과정도 있다. 예측 과정은 추정 과정과는 전혀 다른 별개의
과정으로, 시스템 모델이 중요한 역할을 한다. 그리고 예측하는 변수는 상태변수와 오차
공분산 뿐이다.  

$$
\hat{x}_{k+1}^{-} = A\hat{x}_k
$$  

$$
P_{k+1}^{-} = AP_kA^{T} + Q
$$  

* 칼만 필터를 설계하려면 가장 먼저 대상 시스템을 모델링하여 다음과 같은 형태의 시스템
모델을 구해야 한다. 칼만 필터는 시스템 모델이 실제 시스템과 얼마나 비슷한지에 따라 성능이
크게 달라진다.  

$$
x_{k+1} = Ax_k + w_k
$$

$$
z_k = Hx_k + v_k
$$  

* 시스템 모델을 유도할 때 잡음의 특성도 정확히 파악하도록 노력해야 한다. 칼만 필터에서는
잡음도 중요한 역할을 하기 때문이다. 칼만 필터에서 다루는 잡음은 표준 정규분포를 따르는
백색잡음이다.