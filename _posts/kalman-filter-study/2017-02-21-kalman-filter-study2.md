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

라라라라라ㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇㅇ
