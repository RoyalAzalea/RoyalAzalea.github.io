---
layout: post
title: "Tensorflow Study _ LEC 01 : Machine Learning Basics"
date: 2017-08-01
categories: [tensorflow, machine_learning]
---

## **Machine Learning Basics**

일반적으로 개발자들이 만든 프로그램은 개발자가 만든 대로 동작하는 explicit 프로그램이다.
좋은 프로그램들이 많지만, rule이 많은 상황에서는 이러한 explicit 프로그램을 만들기
어렵다. 1959년에 우리가 일일히 프로그래밍 하지 말고, 프로그램 자체가 어떤 현상이나 자료를
학습을 해서 스스로 무언갈 배워보자는 재밌는 생각이 나왔다. 그리고 이 재밌는 생각이 바로
Machine Learning의 시작이다.  **(Machine Learning:"Field of study that gives computers
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
