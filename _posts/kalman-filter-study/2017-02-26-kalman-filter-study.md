---
layout: post
title: "Kalman Filter Study _ 3장. 저주파 통과 필터"
date: 2017-02-26
categories: [kalman_filter, matlab]
---

저주파 통과 필터(`Low-pass filter`)는 저주파 신호는 통과시키고 고주파 신호는 걸러내는
필터이다. 잡음 제거용으로 많이 쓰이는데, 대게 측정하려는 신호는 저주파이고 잡음은
고주파 성분으로 되어 있기 때문이다.  

저주파 통과 필터 설명에 앞서 이전에 배운 이동평균의 한계를 살펴보자. 이동평균 필터를
실제로 사용해 보면 잡음을 제거하면서 변화 추이를 반영하는게 쉽지 않다. 이제 그 이유가
뭔지 알아보자. 먼저 식 $\ref{2.1}$의 이동평균 정의를 별도의 항으로 풀어 보자.  

$$
\begin{align}
\overline{x_k} &= {x_{k-n+1} + x_{k-n+2} + \cdots + x_k \over n} \\\\
               &= {1 \over n}x_{k-n+1} + {1 \over n}x_{k-n+2} + \cdots + {1 \over n}x_k
\end{align}
$$
