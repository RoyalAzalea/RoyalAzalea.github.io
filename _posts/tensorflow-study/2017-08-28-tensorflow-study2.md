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

- 내부적으로 `W`와 `b`에 대한 값이 업데이트 됨



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


위 결과를 보면, `W`가 1로, `b`는 0으로 다가가고 있음을 볼 수 있다. 아래는
`Placeholder`를 사용하여 `Linear Regression`을 구현한 코드이다.

- 세션을 실행할 때 `list`를 사용하여 한꺼번에 실행시켜줄 수 있음
(ex. `[cost, W, b, train]`)

-  `cost_val`은 `cost`, `W_val`은 `W`, `b_val`은 `b`의 `return`을 받음, 그리고
`_`는 `train`의 `return`을 받으나 굳이 필요하지 않아서 `_`로 씀

- `Placeholder`를 이용하는 큰 이유중 하나는 만든 모델에 직접 값들을 줄 수 있기 때문이다.

- `Placeholder`를 만들 때, `shape = [None]`을 쓰면 1차원 값을 몇개든 받을 수 있다!

- 학습 후 `hypothesis`에 직접 `X` 값을 주면, 학습모델을 통한 예측값 `Y`를 얻을 수 있음


**lab_02_02.py**

```python
# Lab 2 Linear Regression
import time
import tensorflow as tf
tf.set_random_seed(777) # for reproducibility

'''
W와 b 값을 찾기위해 y_data = x_data * W + b 사용
이미 W = 1, b = 0 임을 알고 있지만 TensorFlow가 잘 찾는지 확인해보자
'''
W = tf.Variable(tf.random_normal([1]), name = 'weight')
b = tf.Variable(tf.random_normal([1]), name = 'bias')

'''
X_data와 Y_data대신 Placeholder를 사용하여 만든 모델에
값을 줘보자
'''
X = tf.placeholder(tf.float32, shape=[None])
Y = tf.placeholder(tf.float32, shape=[None])

# Hypothesis 구현
hypothesis = X * W + b

# Cost/loss function 구현
cost = tf.reduce_mean(tf.square(hypothesis - Y))

# Minimize
optimizer = tf.train.GradientDescentOptimizer(learning_rate=0.01)
train = optimizer.minimize(cost)

# Graph의 session을 만들고 시작(graph의 global 변수들 반드시 초기화 해야함!)
sess = tf.Session()
sess.run(tf.global_variables_initializer())

# Fit in line
print("step -- cost -- weight -- bias --")

start_time = time.time()

for step in range(2001):
    cost_val, W_val, b_val, _ = \
    sess.run([cost,W,b,train],
             feed_dict={X: [1, 2, 3, 4, 5], Y:[2.1, 3.1, 4.1, 5.1, 6.1]})

    if step % 20 == 0:
        print("step: %s, cost: %s, W: %s, b: %s " % (step, cost_val, W_val, b_val))

end_time = time.time()

print("total time : ", round((end_time - start_time) * 1000), " ms")

print("-----------testing our model-----------")

print("X : [1.5, 3.5]")
print("Y : %s " % sess.run(hypothesis, feed_dict={X: [1.5, 3.5]}))
```


**result**

```
step -- cost -- weight -- bias --
step: 0, cost: 5.8308, W: [ 2.05798721], b: [-0.85453004]
step: 20, cost: 0.697443, W: [ 1.54248559], b: [-0.85012037]
step: 40, cost: 0.608993, W: [ 1.50494218], b: [-0.72296369]
step: 60, cost: 0.531838, W: [ 1.47186399], b: [-0.60357893]
step: 80, cost: 0.464458, W: [ 1.44096124], b: [-0.49201021]
 ~
step: 1900, cost: 2.05671e-06, W: [ 1.00092793], b: [ 1.09664977]
step: 1920, cost: 1.79626e-06, W: [ 1.00086725], b: [ 1.09686911]
step: 1940, cost: 1.56876e-06, W: [ 1.0008105], b: [ 1.09707403]
step: 1960, cost: 1.37009e-06, W: [ 1.00075734], b: [ 1.0972656]
step: 1980, cost: 1.19689e-06, W: [ 1.00070786], b: [ 1.09744453]
step: 2000, cost: 1.04504e-06, W: [ 1.00066149], b: [ 1.09761178]
total time :  783  ms
-----------testing our model-----------
X : [1.5, 3.5]
Y : [ 2.5986042   4.59992695]
```
