---
layout: post
title: "Tensorflow Study _ Machine Learning Basics / Tensorflow Basics"
date: 2017-08-01
categories: [tensorflow, machine_learning]
---

# Machine Learning Basics  


일반적으로 개발자들이 만든 프로그램은 개발자가 만든 대로 동작하는 explicit 프로그램이다.
좋은 프로그램들이 많지만, rule이 많은 상황에서는 이러한 explicit 프로그램을 만들기
어렵다. 1959년에 우리가 일일히 프로그래밍 하지 말고, 프로그램 자체가 어떤 현상이나 자료를
학습을 해서 스스로 무언갈 배워보자는 재밌는 생각이 나왔다. 그리고 이 재밌는 생각이 바로
Machine Learning의 시작이다. **(Machine Learning:"Field of study that gives computers
the ability to learn without being explicitly programmed" Arthur Samuel (1959))**  


Machine Learning을 하기 위해 프로그램은 학습을 해야한다. 학습의 방법에 따라 `Supervised Learning`과 `Unsupervised Learning`으로 나뉜다.  


`Supervised Learning`의 경우 labeled data/information을
가지고 학습한다. 우리가 일일히 data/information의 label을 나누고 학습을 하도록 한다.  


`Unsupervised Learning`의 경우 un-labeled data/information을 가지고 학습한다. 우리가
일일히 label을 주지 않고, 프로그램이 data/information을 보고 스스로 학습하여 유사한 것들을
모으거나 필요한 작업을 한다.  


Machine Learning을 하기 위해 프로그램이 학습 할 data가 필요하다. 보통 이 data를
`Traning Data Set`이라고 하며, Machine Learning에서 `Traning Data Set`을 사용하여 학습을
통해 어떤 모델을 만들게 된다. 그리고 이 모델을 통해 어떤 input에 대한 output을 얻을 수 있다.  


일반적으로 Machine Learning에서 `Supervised Learning`이 많이 쓰인다. `Supervised Learning`에는
크게 세 종류가 있다. 점수처럼 측정이 되는 `Regression`, pass/non-pass처럼 결정이 되는
`Binary Classification`, 그리고 학점처럼 등급이 나오는 `Multi-label Classfication`이 있다.





# Tensorflow Basics  

## 1. Introduction  

Tensorflow는 Machine Learning을 하기위한 open-source 라이브러리로 현재 많은 ML
라이브러리 중 가장 많은 사람들이 사용하고 있다. Tensorflow의 특징은 다음과 같다.  

 * `data flow graphs`를 통한 `numerical computation`, 풍부한 표현력, 직관적인
 data 흐름 인식  
 * 코드 수정 없이 CPU/GPU mode 동작
 * 계산 구조와 목표 함수 정의를 통한 자동 미분 계산 처리
 * python/C++ 및 다양한 언어 지원



## 2. Terminology

Ternsorflow 공부에 앞서, Tensorflow에서 사용되는 기본 용어들을 살펴보겠다.  


**Operation(op)**


graph 상의 node는 operation(op)라고 불린다. operation은 하나 이상의 tensor를
받을 수 있다. 그리고 operation은 계산을 수행하고, 결과를 하나 이상의 tensor로
반환할 수 있다.


**Tensor**  


내부적으로 모든 데이터는 tensor로 표현된다. tensor는 일종의 다차원 배열로,
graph 내의 operation 간에는 tensor만이 전달된다.  


**Session**  


graph를 실행하기 위해서는 session 객체가 필요하다. session은 operation의 실행
환경을 캡슐화 한것이다.  


**Variables**  


variables는 graph 실행시, parameter를 저장하고 갱신하는데 사용된다. 메모리 상에서
tensor를 저장하는 버퍼 역할을 한다.  


## 3. data flow graphs

Data flow graphs는 node와 edge를 사용한 방향 그래프(`Directed Graph`)이다. node는
수학적 계산, 데이터 입/출력, 그리고 데이터의 읽기/저장 등의 작업을 수행한다. edge는
node들 간 데이터의 입출력 관계를 나타낸다.  

![data_flow_graph](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/tensorflow-study/data_flow_graph.gif)    

간략히 data flow graphs에서 node들은 하나의 operation을 뜻하고, edge는 data
arrays(tensor)를 뜻한다고 생각할 수 있다. 그리고 tensor들이 edge를 통해 돌아다니면서
설정한 작업들이 진행된다.


## 4. Tensorflow Mechanism

Tensorflow Mechanism을 이해하기 위해 아래 그림을 보자.   

![Tensorflow_Mechanism](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/tensorflow-study/Tensorflow_Mechanism.PNG)   

그림의 순서와 같이 처음 operation을 이용하여 graph를 build 한다. 그 다음
session을 통해 graph를 실행하고 graph를 통해 variables return/update 한다.  


## 5. Examples

간단하게 Tensorflow를 통해 Hello World를 출력하는 예제를 살펴보겠다. `hello`라는
constant node를 만들고, `sess`라는 session을 만들어 `run` 하여 실행한다.

**code**

```python
import tensorflow as tf
# hello test
print("------------------------------------------------------------")
print("@ Hello Test")

hello = tf.constant('Hello, TensorFlow!')
sess = tf.Session()
print(sess.run(hello))
```

**result**

```
------------------------------------------------------------
@ Hello Test
b'Hello, TensorFlow!'
```


출력 결과를 보면 `b`라는 문자가 나오는데 이건 `Bytes Literals`라고 알려주는 것
뿐이다.  


다음 예제는 Tensorflow version을 확인하고, session 사용/미사용 시
어떠한 출력을 해주는지 보는 예제이다.

**code**

```python
import tensorflow as tf
# Check TF version
print("------------------------------------------------------------")
print("@ Tensorflow version check")
print(tf.__version__)

# Computational Graph
print("------------------------------------------------------------")
print("@ Not using session")
node1 = tf.constant(3.0, tf.float32)
node2 = tf.constant(4.0) # also tf.float32 implicitly
node3 = tf.add(node1, node2)

print("node1: ", node1, "node2: ", node2)
print("node3: ", node3)

print("@ Using session")

sess = tf.Session()
print("sess.run(node1, node2): ", sess.run([node1, node2]))
print("sess.run(node3): ", sess.run(node3))
```


**result**

```
------------------------------------------------------------
@ Tensorflow version check
1.0.1
------------------------------------------------------------
@ Not using session
node1:  Tensor("Const_1:0", shape=(), dtype=float32) node2:  Tensor("Const_2:0", shape=(), dtype=float32)
node3:  Tensor("Add:0", shape=(), dtype=float32)
@ Using session
sess.run(node1, node2):  [3.0, 4.0]
sess.run(node3):  7.0
```


결과를 보면, session을 사용하지 않은 경우 출력으로 하나의 tensor라고 알려만 준다.
그러므로 어떠한 연산을 진행하고 결과값을 보려면 session을 만들어 node를 실행해야
한다.  


다음 예제는 `Placeholder`를 사용하는 예제이다. `Placeholder`는 미리 그래프를
만들어 놓고 실행시키는 단계에서 값들을 주고 싶을 때 사용한다. node를 만드는
단계에서 `Placeholder`를 사용하여 만들며, 값을 줄 때는 `feed_dict`를 통해
각 node에 값을 전해준다.(참고로 배열도 가능!)


**code**

```python
import tensorflow as tf
# Placeholder
print("------------------------------------------------------------")
print("@ Placeholder & feed_dict test")

a = tf.placeholder(tf.float32)
b = tf.placeholder(tf.float32)
adder_node = a + b # + provides a shortcut for tf.add(a, b)

print(sess.run(adder_node, feed_dict={a: 3, b: 4.5}))
print(sess.run(adder_node, feed_dict={a: [1,3], b: [2, 4]}))

print("@ add_and_triple test")
add_and_triple = adder_node * 3.
print(sess.run(add_and_triple, feed_dict = {a: 3, b: 4.5}))
```


**result**

```
------------------------------------------------------------
@ Placeholder & feed_dict test
7.5
[ 3.  7.]
@ add_and_triple test
22.5
```
