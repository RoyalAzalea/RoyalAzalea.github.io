---
layout: post
title: "Tensorflow Study _ LAB 02 : Linear Regression"
date: 2017-08-28
categories: [tensorflow, machine_learning]
---

`Tensorflow`를 사용하여 `Linear Regression`을 구현해보자. 구현 과정은 다음과 같다.

1. Graph build
2. Session run
3. Update


## 1. Graph build

- Hypothesis
  - `tf.Variable`을 이용하여 `Weight`과 `Bias`를 랜덤으로 생성
  - `random_normal`을 사용할 때 `shape`을 설정해야 함(ex. [1], [1,2])
  - `Tensorflow` 에서 `Variable`은 기존 프로그래밍 언어의 변수들과는 조금 다른
  개념이다. `Ternsorflow`가 사용하는 변수로 자체적으로 변경한다.
  `trainable variable`로 봐도 된다.

- Cost function
  - `tf.square`를 통해 제곱을 표현
  - `reduce_mean`이라는 평균을 내주는 함수를 사용
  - `train`이라는 이름의 노드로 표현

- Minimize
  - `cost`를 최소화 하기 위해 `GradientDescent`를 사용(여러 방법중 하나로, 현재는 마법처럼 생각하기!)
  - `learning_rate`는 최소값을 찾기위해 이동하는 단계를 의미


## 2. Session run

- 세션을 만든 이후 실행하기 전에 `Tensorflow`의 글로벌 변수 초기화를 시켜줘야 함!
  - `tf.global_variables_initailizer()` 사용

- 2000번의 step 중 20번에 한번 씩 출력하도록 세팅
- `train`을 실행시키면 `cost`를 따라 `cost`에 붙은 `hypothesis`등 여러 노드들이 실행됨

## 3. update

- 내부적으로 `W`와 `b`에 대한 값이 업데이트된다.



**lab_02_01.py**

```python
# Lab 2 Linear Regression
import time
import tensorflow as tf
tf.set_random_seed(777) # for reproducibility

# Setting X and Y data
x_train = [1,2,3]
y_train = [1,2,3]

'''
W와 b 값을 찾기위해 y_data = x_data * W + b 사용
이미 W = 1, b = 0 임을 알고 있지만 TensorFlow가 잘 찾는지 확인해보자
'''
W = tf.Variable(tf.random_normal([1]), name = 'weight')
b = tf.Variable(tf.random_normal([1]), name = 'bias')

# Hypothesis 구현
hypothesis = x_train * W + b

# Cost/loss function 구현
cost = tf.reduce_mean(tf.square(hypothesis - y_train))

# Minimize
optimizer = tf.train.GradientDescentOptimizer(learning_rate=0.01)
train = optimizer.minimize(cost)

# Graph의 session을 만들고 시작(graph의 global 변수들 반드시 초기화 해야함!)
sess = tf.Session()
sess.run(tf.global_variables_initializer())

# Fit in line
start_time = time.time()

for step in range(2001):
    sess.run(train)
    if step % 20 == 0:
        print("step: %s, cost: %s, W: %s, b: %s " % (step, sess.run(cost), sess.run(W), sess.run(b)))

end_time = time.time()

print("total time : " , round((end_time - start_time)*1000), " ms")
```


**result**

```
step: 0, cost: 2.82329, W: [ 2.12867713], b: [-0.85235667]
step: 20, cost: 0.190351, W: [ 1.53392804], b: [-1.05059612]
step: 40, cost: 0.151357, W: [ 1.45725465], b: [-1.02391243]
 ~
step: 1920, cost: 1.77484e-05, W: [ 1.00489295], b: [-0.01112291]
step: 1940, cost: 1.61197e-05, W: [ 1.00466311], b: [-0.01060018]
step: 1960, cost: 1.46397e-05, W: [ 1.004444], b: [-0.01010205]
step: 1980, cost: 1.32962e-05, W: [ 1.00423515], b: [-0.00962736]
step: 2000, cost: 1.20761e-05, W: [ 1.00403607], b: [-0.00917497]
total time :  361  ms

```

**lab_02_02.py**

```python
```


**result**

```
```
