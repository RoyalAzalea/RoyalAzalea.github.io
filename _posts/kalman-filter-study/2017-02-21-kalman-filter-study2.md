---
layout: post
title: "Kalman Filter Study _ 1장. 평균 필터"
date: 2017-02-21
categories: [kalman_filter, matlab]
---

평균은 데이터의 총합을 데이터 개수로 나눈 값이다. 예를 들어 $k$개의 데이터가 있을때,
평균은 다음과 같이 계산한다.

$$
\bar{x_k} = { x_1 + x_2 + \cdots + x_k \over k } \label{1.1}\tag{1.1}
$$

위 식은 데이터를 모두 모아서 한꺼번에 계산하는 식으로 배치식(`batch expression`)이다.
식 $1.1$ $\label{1.1}$
