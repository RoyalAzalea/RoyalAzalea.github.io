---
layout: post
title: "Kalman Filter Study _ 13장. 수평 자세 측정_2부"
date: 2017-03-29
categories: [kalman_filter, matlab]
---

## 13.3 센서 융합을 통한 자세 결정

지금까지 자이로와 가속도계로 자세를 결정하는 방법과 결과를 분석해 봤다. 그 결과 두 센서
모두 단독으로 사용하기에는 약점이 있었다. 물론 고가의 센서를 쓰면 되겠지만 우리의 관심사항은
아니다.  

그런데 분석 결과를 찬찬히 검토해 보면 두 센서가 상호 보완 관계인 것을 알 수 있다.
가속도계로 구한 자세는 시간이 지나도 오차가 커지지 않고 일정 범위로 제한되는 장점을 가졌다.
반면 자이로는 자세 변화는 잘 감지하지만 시간이 지나면 오차가 누적되어 발산하는 문제가
있었다. **즉 단기적으로는 자이로 자세가 더 낫지만, 중장기적으로는 가속도 자세가 더 좋다.**
그렇다면 자이로의 누적 오차 문제를 가속도계로 보정하면 좋지 않을까? 빙고! 이게 바로
센서 융합이다.  

여러 센서의 출력을 모아서 더 좋은 성능을 끌어내는 기법을 '센서융합(`Sensor Fusion`)'
이라고 한다. '센서 결합' 이라는 용어도 많이 쓴다. 그런데 센서를 융합한다고 해서 아무 센서나
조합하면 안된다. **센서 융합의 목적은 개별 센서로는 불가능한 성능을 내겠다는 것인데, 센서
구성이 나쁘면 시너지 효과를 기대하기 어렵기 때문이다.**  

**융합용 센서를 구성할 때는 특성을 잘 분석해서 상호 보완이 되는 센서끼리 묶는 것이 중요하다.
이 센서의 단점을 다른 센서의 장점으로 대체할 수 있도록 센서를 결합해야 한다는 뜻이다.**
이렇게 해서 최종 출력에는 각 센서의 장점만 담겠다는 것이 센서 융합의 기본 전략이다. 융합이
성공하면 개별 센서를 뛰어넘는 성능이 나오는 이유가 바로 여기에 있다.  

아래 그림은 자이로와 가속도계의 센서 융합 원리를 개념적으로 표현한 것이다. **가속도계로
결정한 자세가 칼만 필터의 측정값이 된다. 즉, 자이로의 자세 오차를 가속도계로 보정하도록
했다. 왜냐하면 각속도 자세는 발산해서 보정값으로 사용하기에 적합하지 않기 때문이다.**  

![ARS_sub2](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/ARS_sub2.PNG)  

## 13.3.1 시스템 모델  

앞 장의 칼만 필터 예제를 보면 문제가 달라도 알고리즘 자체는 바뀌지 않았다. 모델만
달라졌다. 마찬가지로 센서 융합 문제에서도 바뀌는 것은 모델이고, 칼만 필터 알고리즘은
똑같다. **그러므로 어떻게 시스템 모델을 선정하는지에 더 많은 관심을 가지고 내용을
읽어가는 것이 바람직하다. 그래야 나중에 여러분의 문제에 칼만 필터를 응용할 능력이 생긴다.
칼만 필터를 적용할 때 가장 중요한 것은 적합하고 정확한 시스템 모델을 찾아내는 일이다.**  

그럼 자이로와 가속도계의 센서 융합에 필요한 시스템 모델을 유도해 보겠다. 제대로 하려면
동역학에 대한 약간의 사전 지식이 필요하지만, '숲을 보지 못하는' 잘못을 범하지 않도록
결과 위주로 설명하겠다.  

우리가 관심 있는 물리량은 자세이다. 따라서 자세를 상태변수로 잡는 것은 지극히 자연스러운
선택이다.  

$$
x = \begin{Bmatrix} \phi \\\\ \theta \\\\ \varphi \end{Bmatrix}
$$  

예제에서는 수평 자세($\phi, \theta$)만 관심 있다고 했다. 따라서 요각($\varphi$)은
상태변수로 잡을 필요가 없지만, 수식을 설명하는데 필요해 그냥 나뒀다. 구현할 때는 요각에
아무 값이나 지정해도 상관없다.  

이제 시스템 모델을 유도해보겠다. 앞에서 이미 자이로 각속도와 오일러각 사이의 관계식을
소개한 적이 있다.  

$$
\begin{bmatrix} \dot{\phi} \\\\ \dot{\theta} \\\\ \dot{\varphi} \end{bmatrix} = \begin{bmatrix} 1 & \sin{\phi}\tan{\theta} & \cos{\phi}\tan{\theta} \\\\ 0 & cos{\phi} & -\sin{\phi} \\\\ 0 & \sin{\phi}/\cos{\theta} & \cos{\phi}/\cos{\theta} \end{bmatrix} \begin{bmatrix} p \\\\ q \\\\ r \end{bmatrix} \label{13.1}\tag{13.1}
$$  

**그런데 이 식을 시스템 모델로 삼기에는 심각한 문제가 있다. 시스템 모델이 되려면 다음과
같은 형태를 갖춰야 하는데, 위의 식은 이렇게 표현할 수가 없다. 행렬 안에 있는 오일러각을
밖으로 빼낼 방법이 없기 때문이다.**  

$$
x_{k+1} = Ax_k + w_k \Leftrightarrow \begin{Bmatrix} \dot{\phi} \\\\ \dot{\theta} \\\\ \dot{\varphi} \end{Bmatrix} = \begin{bmatrix} & & \\\\ & \bullet & \\\\ & & \end{bmatrix} \begin{Bmatrix} \phi \\\\ \theta \\\\ \varphi \end{Bmatrix} + w
$$  

이럴 때는 어떻게 해야 할까? 고민할 것 없다. 칼만 필터를 쓰지 못한다. **무슨 수를 쓰더라도
모양을 맞춰야 한다. 이때 쓸 수 있는 방법이 상태변수를 다르게 잡아보는 것이다.**  

오일러각 대신 쿼터니언(`Quaternion`)을 상태변수로 잡아보겠다. 우리의 관심은 쿼터니언이
아니므로, 이에 대한 설명은 생략한다. 그냥 자세를 표현하는 방법 중의 하나라고만 언급해
두겠다.  

$$
x = \begin{Bmatrix} q_1 \\\\ q_2 \\\\ q_3 \\\\ q_4 \end{Bmatrix} \label{13.5}\tag{13.5}
$$  

그런데 쿼터니언과 각속도의 관계는 이미 잘 알려져 있다.  

$$
\begin{Bmatrix} \dot{q_1} \\\\ \dot{q_2} \\\\ \dot{q_3} \\\\ \dot{q_4} \end{Bmatrix} = {1 \over 2}\begin{bmatrix} 0 & -p & -q & -r \\\\ p & 0 & r & -q \\\\ q & -r & 0 & p \\\\ r & q & -p & 0 \end{bmatrix}\begin{Bmatrix} q_1 \\\\ q_2 \\\\ q_3 \\\\ q_4 \end{Bmatrix} \label{13.6}\tag{13.6}
$$

상태변수만 바꿨을 뿐인데, 칼만 필터의 시스템 모델이 갖춰야 할 요구조건을 만족한다. **자세를
쿼터니언으로 표현하든 오일러각으로 표현하든 물리적인 자세는 똑같다. 하지만 상태변수를
어떻게 잡느냐에 따라 칼만 필터의 적용 여부는 이렇게 달라지기도 한다.**  

식 $\ref{13.6}$을 이산(`Discrete`) 시스템으로 바꾸면, 다음과 같은 시스템 모델을 얻을 수
있다.  

$$
{\begin{Bmatrix} q_1 \\\\ q_2 \\\\ q_3 \\\\ q_4 \end{Bmatrix}}_{k+1} = \left( I + \triangle t \cdot {1 \over 2} \begin{bmatrix} 0 & -p & -q & -r \\\\ p & 0 & r & -q \\\\ q & -r & 0 & p \\\\ r & q & -p & 0 \end{bmatrix} \right) {\begin{Bmatrix} q_1 \\\\ q_2 \\\\ q_3 \\\\ q_4 \end{Bmatrix}}_k \label{13.7}\tag{13.7}
$$  

위의 식에서 시스템 모델의 행렬 $A$는 다음과 같이 정의된다.  

$$
A = I + \triangle \cdot {1 \over 2} \begin{bmatrix} 0 & -p & -q & -r \\\\ p & 0 & r & -q \\\\ q & -r & 0 & p \\\\ r & q & -p & 0 \end{bmatrix} \label{13.8}\tag{13.8}
$$  

**그런데 지금까지와는 달리 행렬 $A$의 원소가 상수가 아니고, 각속도가 포함되어 있다.
각속도에 따라 행렬 $A$도 바뀌는 것이다.**  

이제 측정 관계식을 유도해 보겠다. 가속도로 계산한 오일러각을 측정값으로 사용한다는 사실은
이미 이야기했다. 그런데 시스템 모델의 상태변수는 쿼터니언이다. 따라서 측정값도 쿼터니언으로
바꿔서 사용해야 한다. 오일러각을 쿼터니언으로 바꾸는 공식은 다음과 같다.  

$$
\begin{Bmatrix} q_1 \\\\ q_2 \\\\ q_3 \\\\ q_4 \end{Bmatrix} = \begin{Bmatrix} \cos{\phi \over 2}\cos{\theta \over 2}\cos{\varphi \over 2} + \sin{\phi \over 2}\sin{\theta \over 2}\sin{\varphi \over 2} \\\\ \sin{\phi \over 2}\cos{\theta \over 2}\cos{\varphi \over 2} - \cos{\phi \over 2}\sin{\theta \over 2}\sin{\varphi \over 2} \\\\ \cos{\phi \over 2}\sin{\theta \over 2}\cos{\varphi \over 2} + \sin{\phi \over 2}\cos{\theta \over 2}\sin{\varphi \over 2} \\\\ \cos{\phi \over 2}\cos{\theta \over 2}\sin{\varphi \over 2} - \sin{\phi \over 2}\sin{\theta \over 2}\cos{\varphi \over 2} \end{Bmatrix}
\label{13.9}\tag{13.9}
$$  

가속도계로 결정한 자세를 위의 식에 대입하면 쿼터니언으로 표현된 측정값을 얻게 된다. 이
쿼터니언이 칼만 필터의 측정값($z_k$)이 된다. 이 말은 결국 모든 상태변수(즉 쿼터니언)가
측정된다는 뜻이다. 따라서 행렬 $H$는 단위행렬이 된다.  

$$
H = \begin{bmatrix} 1 & 0 & 0 & 0 \\\\ 0 & 1 & 0 & 0 \\\\ 0 & 0 & 1 & 0 \\\\ 0 & 0 & 0 & 1 \end{bmatrix} \label{13.10}\tag{13.10}
$$  

여기까지의 과정이 너무 복잡해서 혼란스럽다면 찬찬히 다시 읽어 보기 바란다. 핵심을 요약하면
아래와 같다.  

**"자이로와 가속도계의 상호 보완적인 특성을 이용해, 가속도계로 자이로를 보상하는 센서
융합을 하려도 한다. 그런데 시스템 모델이 칼만 필터가 적용되지 않는 형태였다. 그래서
상태변수를 오일러각에서 쿼터니언으로 바꾸고, 측정값도 오일러각에서 쿼터니언으로 바꿨다.
그랬더니 시스템 모델이 칼만 필터를 쓸 수 있는 형태가 되었다."**  

## 13.3.2 센서 융합 칼만 필터  

시스템 모델을 모두 구했으니 이제 칼만 필터를 설계해보자. 이제는 말하기도 지겹지만, 시스템
모델이 있으면 칼만 필터의 구현은 기계적으로 따라 하면 된다.  

`EulerKalman.m`은 칼만 필터로 구현한 센서 융합 프로그램이다. 이 함수는 시스템 행렬 $A$ 와
측정값을 인자로 받아 오일러각을 반환한다. 함수의 마지막 부분에 칼만 필터의 쿼터니언
추정값을 오일러각으로 변환하는 코드가 있다. 오일러각과 달리 쿼터니언은 물리적인 의미가
없기 때문에, 추정 결과는 오일러각으로 바꿔서 출력하는 게 더 낫기 때문이다.  

잡음의 공분산 행렬 $Q$와 $R$은 시스템의 신호 특성과 관련 있는 값이라, 이론적으로 구하기는
어렵고 실제 데이터를 분석해봐야 한다. 보통은 이 행렬을 설계인자로 보고, 이런 저런 값을
넣어 성능 변화의 추이를 보면서 결정한다. 여기서는 아래와 같이 선정했다.  

$$
Q = \begin{bmatrix} 0.0001 & 0 & 0 & 0 \\\\ 0 & 0.0001 & 0 & 0 \\\\ 0 & 0 & 0.0001 & 0 \\\\ 0 & 0 & 0 & 0.0001 \end{bmatrix}, R = \begin{bmatrix} 10 & 0 & 0 & 0 \\\\ 0 & 10 & 0 & 0 \\\\ 0 & 0 & 10 & 0 \\\\ 0 & 0 & 0 & 10 \end{bmatrix}
$$

상태변수와 오차 공분산 행렬의 초기값은 다음과 같이 선정했다. 이 상태변수(쿼터니언)
초기값의 물리적인 의미는 오일러각이 모두 $0$이라는 뜻이다.  

$$
\hat{x}_0^{-} = \begin{Bmatrix} q_1 \\\\ q_2 \\\\ q_3 \\\\ q_4 \end{Bmatrix} = \begin{Bmatrix} 1 \\\\ 0 \\\\ 0 \\\\ 0 \end{Bmatrix}, P_0^{-} = \begin{bmatrix} 1 & 0 & 0 & 0 \\\\ 0 & 1 & 0 & 0 \\\\ 0 & 0 & 1 & 0 \\\\ 0 & 0 & 0 & 1 \end{bmatrix}
$$  

**EulerKalman.m**

```matlab
function [phi theta psi] = EulerKalman(A, z)
% 오일러각을 반환하는 칼만 필터 함수
% 시스템 행렬 A와 측정값을 인자로 받아 오일러각을 반환
persistent H Q R
persistent x P
persistent firstRun


if isempty(firstRun)
  H = eye(4);

  Q = 0.0001*eye(4);
  R = 10*eye(4);

  x = [1 0 0 0]';  
  P = 1*eye(4);

  firstRun = 1;  
end


xp = A*x;
Pp = A*P*A' + Q;

K = Pp*H'*inv(H*Pp*H' + R);

x = xp + K*(z - H*xp);     % x = [ q1 q2 q3 q4 ]
P = Pp - K*H*Pp;

% 쿼터니언 추정값을 오일러각으로 변환!
phi   =  atan2( 2*(x(3)*x(4) + x(1)*x(2)), 1 - 2*(x(2)^2 + x(3)^2) );
theta = -asin(  2*(x(2)*x(4) - x(1)*x(3)) );
psi   =  atan2( 2*(x(2)*x(3) + x(1)*x(4)), 1 - 2*(x(3)^2 + x(4)^2) );
```


이제 칼만 필터의 센서 융합 성능을 알아보겠다. 아래 `TestEulerKalman.m`은
`TestEulerKalman` 함수를 테스트하기 위한 프로그램이다. 자이로와 가속도계의 측정값은
앞 절과 동일한 시험 데이터를 사용했다.  

구현 코드를 보면 가장 먼저 식 $\ref{13.8}$에 따라 자이로의 측정 각속도를 가지고 시스템
행렬 $A$를 구성한다. 그 다음에 가속도계로 오일러각을 계산한다. 이 값은
`EulerToQuaternion` 함수를 통해 쿼터니언으로 변환되어, 칼만 필터의 측정값으로 입력된다.
참고로 `EulerToQuaternion` 함수를 호출할 때 요각($\varphi$)을 $0$으로 지정했는데,
어떤 값이든 상관없다.  

**TestEulerKalman.m**

```matlab
clear all

Nsamples = 41500;
EulerSaved = zeros(Nsamples, 3);

dt = 0.01;

for k=1:Nsamples
  % 각속도 읽어옴
  [p q r] = GetGyro();  
  % 측정된 각속도를 이용하여 시스템 행렬 A 구성
  A = eye(4) + dt*1/2*[ 0  -p  -q  -r;
                        p   0   r  -q;
                        q  -r   0   p;
                        r   q  -p   0
                      ];

  % 가속도 읽어옴                
  [ax ay] = GetAccel();
  % 측정된 가속도를 이용하여 오일러각 계산
  [phi theta] = EulerAccel(ax, ay);
  % 오일러각을 이용하여 쿼터니언각 계산
  z = EulerToQuaternion(phi, theta, 0);

  [phi theta psi] = EulerKalman(A, z);

  EulerSaved(k, :) = [ phi theta psi ];
end

% radian -> degree
PhiSaved   = EulerSaved(:, 1) * 180/pi;
ThetaSaved = EulerSaved(:, 2) * 180/pi;
PsiSaved   = EulerSaved(:, 3) * 180/pi;

t = 0:dt:Nsamples*dt-dt;

% 칼만 필터를 이용하여 구한 값 확인
figure
plot(t, PhiSaved)
xlabel('Time[sec]')
ylabel('Roll angle[deg]')

figure
plot(t, ThetaSaved)
xlabel('Time[sec]')
ylabel('Pitch angle[deg]')

figure
plot(t, PsiSaved)
xlabel('Time[sec]')
ylabel('Yaw angle[deg]')
```

**EulerToQuaternion.m**

```matlab
function z = EulerToQuaternion(phi, theta, psi)
% 오일러각을 쿼터니언각으로 변환하는 함수
%
sinPhi   = sin(phi/2);    cosPhi   = cos(phi/2);
sinTheta = sin(theta/2);  cosTheta = cos(theta/2);
sinPsi   = sin(psi/2);    cosPsi   = cos(psi/2);

z = [ cosPhi*cosTheta*cosPsi + sinPhi*sinTheta*sinPsi;
      sinPhi*cosTheta*cosPsi - cosPhi*sinTheta*sinPsi;
      cosPhi*sinTheta*cosPsi + sinPhi*cosTheta*sinPsi;
      cosPhi*cosTheta*sinPsi - sinPhi*sinTheta*cosPsi;
    ];
```


센서 융합 결과를 보겠다. 다음 그래프에 롤각과 피치각의 궤적을 그렸다. $\pm30^\circ$ 의
움직임이 정확하게 측정되었다. 자이로와 가속도계의 결과와 비교해보면 놀라운 결과가 아닐 수
없다. 애초의 설계 목표대로 자이로의 누적 오차가 없어졌고, 가속도계의 부정확성도
제거되었다. 두 센서의 장점이 잘 융합된 덕분이다.  

![ARS_8](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/ARS_8.PNG)  

![ARS_9](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/ARS_9.PNG)  

![ARS_10](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/ARS_10.PNG)  

이상에서 자이로와 가속도계의 상호 보완적인 특징을 이용하여 두 센서를 융합하면 훨씬 더
좋은 성과를 거둘 수 있음을 확인했다. 특히 칼만 필터에 적합한 시스템 모델만 구하면 센서
융합 문제도 동일한 칼만 필터 알고리즘으로 해결 가능했다.  

하지만 일반적인 센서 융합 문제에서 시스템 모델이 칼만 필터가 요구하는 선형 시스템 모델로
표현되는 경우는 드물다. 이 장에서 다룬 자이로와 가속도계의 융합이 오히려 특별한 경우이다.
**따라서 칼만 필터를 보다 다양한 센서 융합 문제에 적용하려면 더 일반적인 방법이
필요하다.** 이 문제는 뒤에서 다시 다뤄보겠다.  
