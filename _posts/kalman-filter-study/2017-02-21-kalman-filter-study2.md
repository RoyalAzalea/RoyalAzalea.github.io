---
layout: post
title: "Kalman Filter Study _ 1장. 평균 필터"
date: 2017-02-21
categories: [kalman_filter, matlab]
---

평균은 데이터의 총합을 데이터 개수로 나눈 값이다. 예를 들어 $k$개의 데이터가 있을때,
평균은 다음과 같이 계산한다.

$$
\overline{x_k} = { x_1 + x_2 + \cdots + x_k \over k } \label{1.1}\tag{1.1}
$$

위 식은 데이터를 모두 모아서 한꺼번에 계산하는 식으로 배치식(`batch expression`)이다.
이 배치식에 데이터를 하나 추가하려면 모든 데이터를 다시 더해서 $k+1$로 나눠야 한다.
이 과정은 앞서 계산한 평균($\overline{x_k}$)을 전혀 써먹지 못한다. 그리하여 앞서 계산한
평균을 활용할 수 있는 재귀식이 필요하다.  

재귀식은 이전 평균값과 추가된 데이터 그리고 데이터 개수만 저장하면 된다. 데이터의 양이
늘어나면 배치식 보다 재귀식이 메모리 저장공간의 측면과 계산성능 측면에서 유리하다.
이제 식 \ref{1.1}을 재귀식으로 바꿔보자. 재귀식을 유도하는 과정에 필요한 $k-1$개의
평균 계산식을 사용한다.

$$
\overline{x_{k-1}} = { x_1 + x_2 + \cdots + x_{k-1} \over k-1 } \label{1.2}\tag{1.2}
$$

식 \ref{1.1}의 양변에 $k$를 곱한다.

$$
k\overline{x_k} = x_1 + x_2 + \cdots + x_k
$$

이 식의 양변을 다시 $k-1$로 나누면 다음과 같은 식이 된다.

$$
{k \over k-1}\overline{x_k} = { x_1 + x_2 + \cdots + x_k \over k-1 }
$$

여기서 우변의 $x_k$를 따로 분리해서 두개의 항으로 만들면 다음의 식이 된다.

$$
{k \over k-1}\overline{x_k} = { x_1 + x_2 + \cdots + x_k \over k-1 } \\\
                            = { x_1 + x_2 + \cdots + x_{k-1} \over k-1 } + { x_k \over k-1 }
$$
