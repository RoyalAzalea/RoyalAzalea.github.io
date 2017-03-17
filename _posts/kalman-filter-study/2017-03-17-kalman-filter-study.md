---
layout: post
title: "Kalman Filter Study _ 11장. 위치로 속도 재기_1부"
date: 2017-03-17
categories: [kalman_filter, matlab]
---

앞 장에서 살펴 본 예제의 목표는 측정 잡음을 제거하는 것이었다. 그런데 잡음 제거라면
저주파 통과 필터도 훌륭하게 해낼 수 있다. 더구나 저주파 통과 필터는 칼만 필터 보다 더
간단한 구조라 구현하기도 쉽다. 이런 저주파 통과 필터를 놔두고 굳이 칼만 필터를 사용할
이유가 있을까?  

그렇다 칼만 필터는 단지 잡음만 제거하는 필터가 아니다. 이제 아래 예제로 칼만 필터의
능력을 살펴보자.  

**"어떤 실험으로 직선 선로에서 열차가 $80m/s$의 속도를 유지하는지 확인하려 한다. 위치와
속도 정보는 $0.1$초 간격으로 측정해서 저장하도록 되어있다. 그런데 시험 결과를 살펴보니
속도 데이터가 모두 $0$으로 찍혀 있다. 실험에 드는 비용과 시간이 만만치 않다. 그나마
다행인 것은 위치 정보는 이상이 없어 보인다. 다시 실험을 하지 않고 이 위기에서 빠져
나갈 수 없을까?"**  

## 11.1 시스템 모델  

이 에제는 그 동안 다뤄온 문제와는 많이 다르다. 지금까지는 주로 측정 잡음을 효과적으로
제거하는 방법을 고민해왔다. 그런데 이제는 위치 정보만 가지고 측정하지도 않은 속도를
알아내야 한다. 일단 저주파 통과 필터로는 이런 유형의 문제를 해결할 수 없다. 저주파 통과
필터는 측정 신호의 잡음만 제거할 수 있기 때문이다.  

얼핏 생각하기에 이 예제가 문제를 위한 문제 같아 보일지 모르지만, 의외로 실전에서 종종
접하게 된다. 예를 들어 고도만 측정했는데 상승률을 계산해야 한다거나 각도만 가지고 회전
속도를 알아내야 하는 상황이 곧잘 있다.  

잘 알다시피 속도는 이동 거리를 시간으로 나눈 값이다. 따라서 짧은 시간 동안의 이동 거리를
시간으로 나누면 그 구간의 속도를 구할 수 있다. 그런데 실제로 이 방법을 적용해보면 오차가
크다. **잡음 때문에 측정 거리는 들쭉날쭉한데 분모인 시간은 아주 작아서, 나누면 값이 크게
튀기 때문이다.** 이러한 문제를 해결하기 위해 몇 개의 측정값을 평균 내서 이동 거리로
사용하거나(이동평균), 측정값을 다항식으로 근사한 다음 그 다항식을 미분하는 등의 방법을
동원하기도 한다. 하지만 이런 해결책은 지저분할 뿐만 아니라, 잘 들어맞지도 않는다. 칼만
필터는 바로 이런 유형의 문제에서 진가를 발휘한다.  

그럼 칼만 필터를 설계해보겠다. 가장 먼저 시스템 모델을 찾아야 한다. **우리가 관심 있는
물리량은 열차의 위치와 속도이므로 이 두 변수를 상태변수로 정의한다.**  

$$
x = \begin{Bmatrix} 위치 \\\\ 속도 \end{Bmatrix}
$$  

이 상태변수에 대해 예제의 시스템 모델은 다음과 같이 잡겠다.  

$$
x_{k+1} = Ax_k + w_k \\\\
z_k = Hx_k + v_k
$$  

$$
A = \begin{bmatrix} 1 & \triangle t \\\\
0 & 1 \end{bmatrix} \\\\
H = \begin{bmatrix} 1 \\\\ 0 \end{bmatrix}
$$  

**여기서 $\triangle t$는 위치를 측정하는 시간 간격을 의미한다.** 이미 언급했듯이 시스템
모델을 찾아내는 것은 예술이자 기술이다. **칼만 필터 설계자의 경험과 능력에 의존할 수 밖에
없다는 말이다.** 참고로 비슷한 문제를 참고하는 것도 좋은 방법이다.  

그럼 위 시스템 모델이 예제의 내용을 제대로 반영하고 있는지 확인해보자. 먼저 행렬 $A$를
시스템 모델에 대입해서 상태변수 관계식을 풀어 보자.  

$$
\begin{align}
x_{k+1} &= Ax_k + w_k \\\\
&= \begin{bmatrix} 1 & \triangle t \\\\ 0 & 1 \end{bmatrix} + w_k
\end{align}
$$  

이 식에 상태변수의 정의를 대입해서 정리하면 더 구체적인 의미를 알 수 있다.  

$$
\begin{align}
\begin{bmatrix} 위치 \\\\ 속도 \end{bmatrix}_{k+1} &= \begin{bmatrix} 1 & \triangle t \\\\ 0 & 1 \end{bmatrix}\begin{bmatrix} 위치 \\\\ 속도 \end{bmatrix}_{k} + \begin{bmatrix} 0 \\\\ w_k \end{bmatrix}
&= \begin{bmatrix} 위치+속도 \dot \triangle t \\\\ 속도 + w \end{bmatrix}
\end{align}
$$  

먼저 위치와 관련된 관계식만 따로 떼어내서 살펴보겠다.  

$$
위치_{k+1} = 위치_{k} + 속도_{k} \dot \triangle t
$$  

이 식은 **'현재 위치 = 직전 위치 + 이동거리'** 라는 물리 법칙을 수식으로 표현한 것이다.
시스템 잡음이 관계식에 포함되지 않은 것은 이 때문이다. 이제 속도 관계식을 살펴보겠다.  

$$
속도_{k+1} = 속도_{k} + w_k
$$  

**이 식이 의미하는 바는 열차 속도는 시스템 잡음($w_k$)의 영향만 받을 뿐, 다른 외부의
힘은 작용하지 않는다는 것이다. 즉 속도가 일정하다는 말이다.** 예제에서 열차의 속도가
일정하다고 했으니 제대로 모델링 되었다. 여기서 시스템 잡음은 마찰, 엔진 제어기의 오차
등 열차 속도에 영향을 주는 모든 요인의 합을 의미한다.  

그럼 이제 시스템 모델의 측정값 관계식을 살펴보겠다. 행렬 $H$를 측정값 관계식에 대입해서
정리한다.  

$$
\begin{align}
z_k &= Hx_k + v_k \\\\
&= \begin{bmatrix} 1 & 0 \end{bmatrix} + v_k \\\\
&= 위치_{k} + v_k
\end{align}
$$  

**이 식이 의미하는 바는 측정하는 값은 위치이고, 측정값에는 잡음이 섞여 있다는 것 이다.**
이러한 사실은 예제의 내용과 정확히 일치한다.  

지금까지 시스템 모델이 실제 시스템과 어떻게 연관되어 있는지를 설명했다. 이처럼 행렬 $A$와
$H$는 임의로 선정한 것이 아니라, 시스템의 물리적인 관계를 모델링한 결과이다. **즉 시스템
모델은 속도와 거리 사이의 물리적 관계, 속도에 영향을 주는 오차 요인 등을 수학식으로
표현한 것이다.**  

마지막으로 잡음의 공분산 행렬($Q, R$)만 결정하면 시스템 모델의 유도는 끝난다. 측정
잡음($v_k$)의 오차 특성은 센서 제작사에서 제공하는 경우가 많다. 그렇지 않다면 실험과
경험을 통해 결정해야 한다. 시스템 잡음($w_k$)의 모델링은 더 어려운데, 시스템에 대한
지식과 경험에 의존할 수 밖에 없다. **만약 두 공분산 행렬을 해석적으로 구하기 어렵다면
이 행렬을 칼만 필터의 설계 인자로 보고 시행착오를 거쳐 선정해야 한다.** 이 예제에서는
두 행렬을 다음과 같이 선정했다.  

$$
Q = \begin{bmatrix} 1 & 0 \\\\ 0 & 3 \end{bmatrix} ,\; R = 10
$$  

## 11.2 칼만 필터 함수  

시스템 모델을 구하는 작업은 모두 끝났으니, 이제 칼만 필터를 구현해보자. 아래
`DvKalman` 함수가 칼만 필터의 구현 코드이다. 이 참수는 측정값을 인자로 받아 위치
추정값과 속도 추정값을 반환한다. 시스템 모델만 바뀌었을 뿐, 알고리즘 구현 코드는 앞 장의
예제에서 거의 달라진게 없다.  

**DvKalman.m**

```matlab
function [ pos vel ] = DvKalman(z)
% 위치로 속도 측정하는 함수
%

persistent A H Q R
persistent x P
persistent firstRun

if isempty(firstRun)
    firstRun = 1;

    dt = 0.1;

    A = [ 1 dt;
          0 1];

    H = [1 0];

    Q = [ 1 0;
          0 3];

    R = 10;

    x = [0 20]'; % 초기위치 0, 초기속도 20
    P = 5*eye(2);
end

xp = A*x;  
Pp = A*P*A' + Q;    

K = Pp*H'*inv(H*Pp*H' + R);

x = xp + K*(z - H*xp);
P = Pp - K*H*Pp;   


pos = x(1);
vel = x(2);

end
```  


## 11.3 추정 결과  

측정한 열차 위치와 칼만 필터가 추정한 위치를 비교해서, 알고리즘이 잘 작동하는지
확인해보자. `TestDvKalman.m`은 앞 절의 칼만 필터 함수를 테스트하기 위한 프로그램이다.  

**TestDvKalman.m**

```matlab
clear all


dt = 0.1;
t  = 0:dt:10;

Nsamples = length(t);

Xsaved = zeros(Nsamples, 2);
Zsaved = zeros(Nsamples, 2);

for k=1:Nsamples
  [z trueV] = GetPos();
  Zsaved(k, :) = [z trueV];

  [pos vel] = DvKalman(Zsaved(k,1));
  Xsaved(k, :) = [pos vel];
end


figure
hold on
plot(t, Zsaved(:, 1), 'r.')
plot(t, Xsaved(:, 1))
xlabel('Time[sec]')
ylabel('Position[m]')
legend('Measurements','Kalman Filter','location','NorthWest')

figure
plot(t, Zsaved(:, 2), 'r:')
hold on
plot(t, Xsaved(:, 2), 'b')
xlabel('Time[sec]')
ylabel('Velocity[m/s]')
legend('True Speed', 'Kalman Filter','location','SouthEast')
```

**GetPos.m**

```matlab
function [z , trueV] = GetPos()
%
%
persistent Velp Posp


if isempty(Posp)
  Posp = 0;
  Velp = 80;
end

dt = 0.1;

w  = 0 +  10*randn;
v  = 0 +  10*randn;

z = Posp + Velp*dt + v;

Posp = z - v;     % true position
Velp = 80 + w;    % true speed

trueV = Velp;
```  


측정 위치는 `GetPos` 함수로 읽어 오는데, 열차 속도가 $80m/s$에서 약간씩 변한다고
가정했다. 실전에서는 `GetPos` 함수 대신 센서로 측정한 위치를 받아온다.  

![DvKalman_1](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/DvKalman_1.PNG)  

위 그래프에서 실선은 칼만 필터의 추정 위치를 나타내고, 점으로 표시된 데이터는 위치
측정값을 의미한다. 측정 잡음을 효과적으로 제거하여 추정 위치의 궤적이 더 매끄러워졌고,
열차의 이동 경향도 잘 나타나 있다.  

![DvKalman_2](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/DvKalman_2.PNG)  

위 그림은 칼만 필터가 추정한 속도를 그린 그래프이다. 그림에서 실선은 칼만 필터가 추정해낸
속도이고, 점선이 측정 잡음을 뺀 열차의 진짜 속도를 나타낸다. 그림을 보면 추정 속도의
경향이 진짜 속도와 아주 비슷하다. 그런데 처음에는 추정 속도 오차가 상당히 크게 나다가,
$2$초 정도 지나서야 비교적 추정값이 정확해진다. 테스트 프로그램에서 초기 속도를 $20m/s$
라는 어이 없는 값을 가정한 탓이다. 그럼에도 불구하고 속도를 정확하게 추정해냈다.  

단지 칼만 필터 알고리즘을 돌렸을 뿐인데, 위치 정보만 가지고도 상당히 정확하게 속도를
알아냈다. 이게 바로 저주파 통과 필터와는 차원이 다른 칼만 필터의 진짜 능력이다.
