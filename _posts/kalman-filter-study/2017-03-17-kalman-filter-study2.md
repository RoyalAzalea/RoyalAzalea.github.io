---
layout: post
title: "Kalman Filter Study _ 11장. 위치로 속도 재기_2부"
date: 2017-03-17
categories: [kalman_filter, matlab]
---

## 11.4 속도로 위치 측정하기

이 절에서는 앞의 예제를 반대로 뒤집어 보겠다. 속도를 측정해서 위치를 추정해 보는 것이다.
우선 측정값이 위치에서 속도로 변경되었으니 시스템 모델에서 행렬 $H$가 달라져야 한다.  

$$
H = \begin{bmatrix} 0 & 1 \end{bmatrix}
$$  

이 값을 시스템 모델에 대입해서 정리해서 보면, 행렬 $H$가 왜 이렇게 바뀌는지 이해가 된다.  

$$
\begin{align}
z_k &= Hx_k + v_k \\\\
&= \begin{bmatrix} 0 & 1 \end{bmatrix}\begin{bmatrix} 위치 \\\\ 속도 \end{bmatrix}_{k} + v_k \\\\
&= 속도_{k} + v_k
\end{align}
$$  

측정값이 속도라는 시스템 변경사항이 제대로 반영되었다. 그 밖에 시스템 모델에서 바뀌는
사항은 없다. 다음은 변경된 모델에 대한 칼만 필터 함수이다. 행렬 $H$를 초기화하는 코드를
제외하고는 똑같다.  

**IntKalman.m**

```matlab
function [ pos vel ] = IntKalman(z)
% 속도로 위치 측정하는 함수
%

persistent A H Q R
persistent x P
persistent firstRun


if isempty(firstRun)
  firstRun = 1;

  dt = 0.1;

  A = [ 1 dt;
        0 1  ];
  H = [0 1];

  Q = [ 1 0;
        0 3 ];
  R = 10;

  x = [ 0 20 ]';
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


테스트 프로그램도 앞의 예제와 거의 비슷하다. `GetVel` 함수로 측정 속도를 읽어오고,
새로운 칼만 필터를 호출하는 부분만 바뀌었다. 거리 대신 속도를 반환하는 점만 다를 뿐,
`GetVel` 함수에서 속도와 거리를 생성해내는 방법도 동일하다.  

**TestIntKalman.m**

```matlab
clear all


dt = 0.1;
t  = 0:dt:10;

Nsamples = length(t);

Xsaved = zeros(Nsamples, 2);
Zsaved = zeros(Nsamples, 1);

for k=1:Nsamples
  z = GetVel();      
  [pos vel] = IntKalman(z);

  Xsaved(k, :) = [pos vel];
  Zsaved(k)    = z;
end


figure
plot(t, Xsaved(:, 1))
xlabel('Time[sec]')
ylabel('Position[m]')

figure
hold on
plot(t, Zsaved(:), 'r.')
plot(t, Xsaved(:, 2))
xlabel('Time[sec]')
ylabel('Velocity[m/s]')
legend('Measurement','Kalman Filter','location','NorthWest')
```

**GetVel.m**

```matlab
function z = GetVel()
%
%
persistent Velp Posp


if isempty(Posp)
  Posp = 0;
  Velp = 80;
end

dt = 0.1;

v  = 0 +  10*randn;

Posp = Posp + Velp*dt;     % true position
Velp = 80 + v;             % true speed

z = Velp;
```  


그럼 추정 결과를 보겠다. 아래 그래프에 측정 속도와 추정 속도를 비교했다. 실선이 추정
속도를 나타내고, 점은 속도 측정값을 의미한다. 측정 잡음이 많이 제거되었고, 기준 속도인
$80m/s$에 근처 추정 속도가 모여 있다. 만족할 만한 결과이다.  

![DvKalman_3](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/DvKalman_3.PNG)  

다음 그림은 추정 위치를 그린 그래프이다. 위치는 측정하지 않았지만 상당히 괜찮은 추정
결과를 얻었다. 평균 속도가 $80m/s$이니까 $10$초에 $800m$ 정도를 가는 게 정상이다.
그래프를 보면 $10$초 후에 $800m$ 정도 이동한 게 보인다.  

![DvKalman_4](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/DvKalman_4.PNG)  

칼만 필터는 이렇게 시스템 모델에 따라 용도가 달라진다.  

## 11.5 초음파 거리계로 속도 재기  

앞의 예제에서는 속도가 일정했다. 그 덕분에 칼만 필터가 속도를 추정해 낼 수 있었던 것
아니냐고 의심할 수 있다. 그래서 이 절에서는 속도가 일정하지 않은 경우에도 위치로 속도를
추정해 낼 수 있는지 알아보겠다. 예제로는 이전에 다뤘던 초음파 거리계를 다시 사용한다.
이 예제는 거리의 변화 즉 속도가 일정하지 않다.  

먼저 칼만 필터의 시스템 모델을 선정해야 하는데, 앞의 에제와 동일한 모델을 사용한다.
**시스템의 기동은 많이 다르지만, 시스템 모델은 같다. 거리와 속도의 물리적인 관계는
변하지 않는 진리이기 때문이다.**  

칼만 필터 함수도 앞의 `DvKalman` 함수를 그대로 사용한다.  

**TestDvKalman2.m**

```matlab
clear all

Nsamples = 500;

Xsaved = zeros(Nsamples, 2);
Zsaved = zeros(Nsamples, 1);

for k=1:Nsamples
  z = GetSonar();      
  [pos vel] = DvKalman(z);

  Xsaved(k, :) = [pos vel];
  Zsaved(k)    = z;
end


dt = 0.02;
t  = 0:dt:Nsamples*dt-dt;

figure
hold on
plot(t, Zsaved(:), 'r.')
plot(t, Xsaved(:, 1))
xlabel('Time[sec]')
ylabel('Distance[m]')
legend('Measurement','Kalman Filter','location','NorthWest')

figure
plot(t, Xsaved(:, 2))
hold on
plot(t, (Xsaved(:, 1)-40)/2,'r:')
xlabel('Time[sec]')
ylabel('Velocity[m/s]')
legend('Velocity','Distance','location','NorthWest')
```

**GetSonar.m**

```matlab
function h = GetSonar()
%
%
persistent sonarAlt       % SonartAlt.mat
persistent k firstRun


if isempty(firstRun)
  load SonarAlt
  k = 1;

  firstRun = 1;
end

h = sonarAlt(k);

k = k + 1;
```

다음 그림은 거리의 궤적을 비교한 그래프이다. 칼만 필터의 추정값은 실선으로 그렸고,
측정 거리는 점으로 표시했다. 측정 잡음이 많이 제거 되었고, 거리의 변화 추이도 잘 드러나
있다. 여러 차례 설명했듯이 잡음 제거는 칼만 필터의 기본 기능이다. 그런데 그래프를 자세히
보면 시간 지연이 거의 보이지 않는다. 숙원이던 잡음 제거와 변화 민감성이라는 두 가지
목표를 달성해낸 것이다.  

![DvKalman_5](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/DvKalman_5.PNG)  

다음 그래프는 칼만 필터가 추정한 속도를 그렸다. 그래프에서 실선은 추정 속도의 궤적이고,
점선은 추정 거리를 나타낸다. 추정 속도와 같은 그래프에 그리기 위해 추정 거리에서 $40$
미터를 빼고 크기를 반으로 줄였다. 추정 거리의 변화율과 추정 속도를 비교해 보면 추정
속도가 나름 타당하다는 것을 알 수 있다. **거리의 변화가 별로 없는 구간에서는 속도가 $0$에
가깝고, 변화가 심한 구간에서는 속도 값이 크게 나타난다. 거리의 변화율이 속도에 비례한다는
사실을 염두에 두고 찬찬히 비교해 보면 된다.**  

![DvKalman_6](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/DvKalman_6.PNG)  

## 11.6 효율적인 칼만 필터 함수  

이 절에서는 간단한 팁을 하나 소개하겠다. 5장 그림의 칼만 필터 알고리즘을 보면 칼만 이득을
계산할 때 역행렬을 한 번 계산해야 한다. 역행렬은 보통 수치해석 기법으로 구하는데,
계산시간이나 안정성을 고려하면 되도록 피하는 게 좋다. 특히 빠르게 실행되는 실시간
시스템에 칼만 필터를 적용할 때는 어떻게든 계산시간을 단축하는 것이 바람직하다. **이 장의
예제와 같이 시스템 모델의 행렬이 작으면 행렬 계산식을 풀어 쓰는 것도 계산시간과 오차를
줄이는 데 도움이 된다.** 그럼 칼만 이득을 계산하는 행렬식을 풀어 써보겠다.  

$$
K_k = P_k^{-}H^{T}(HP_k^{-}H^{T} + R)^{-1}
$$  

먼저 반복해서 등장하는 $P_k^{-}H^{T}$ 계산부터 시작한다. 행렬은 앞 절의 시스템 모델을
그대로 사용한다.  

$$
\begin{align}
P_k^{-}H^{T} &= \begin{bmatrix} P_11^{-} & P_12^{-} \\\\ P_21^{-} & P_22^{-}  \end{bmatrix}\begin{bmatrix} 1 \\\\ 0 \end{bmatrix} \\\\
&= \begin{bmatrix} P_11^{-} \\\\ P_21^{-} \end{bmatrix}
\end{align}
$$  

이제 이 식을 활용해서 역행렬을 계산하는 행렬식 전체를 정리해 보겠다.  

$$
\begin{align}
HP_k^{-}H^{T} + R &= \begin{bmatrix} 1 & 0 \end{bmatrix}\begin{bmatrix} P_11^{-} & P_21^{-} \end{bmatrix} + R \\\\
&= P_11^{-} + R
\end{align}
$$  

상당히 간단해졌다. 더구나 풀어 써보니 행렬인 줄 알았던 수식이 스칼라로 드러났다. 스칼라의
역행렬은 역수니까 계산도 굉장히 쉽다. 이제 위의 결과를 칼만 이득 계산식에 대입해서 정리해
보겠다.  

$$
\begin{align}
K_k &= P_k^{-}H^{T}(HP_k^{-}H^{T} + R)^{-1} \\\\
&= \begin{bmatrix} P_11^{-} \\\\ P_21^{-} \end{bmatrix}(P_11^{-} + R)^{-1} \\\\
&= {1 \over P_11^{-} + R}\begin{bmatrix} P_11^{-} \\\\ P_21^{-} \end{bmatrix}
\end{align}
$$  

식이 매우 간단해졌다. 그럼 이 식이 과연 칼만 필터 알고리즘의 행렬식을 대신할 수 있는지
확인해보겠다. 위의 식으로 칼만 이득을 계산하는 `DeDvKalman` 함수를 새로 작성했다.
`TestDeDvKalman.m`은 `DeDvKalman` 함수를 테스트 하는 프로그램이다.  

**DeDvKalman.m**

```matlab
function [pos vel] = DeDvKalman(z)
% 행렬이 작은 경우 역행렬을 풀어서 사용하는 함수
%
persistent A H Q R
persistent x P
persistent firstRun


if isempty(firstRun)
  firstRun = 1;

  dt = 0.1;

  A = [ 1 dt;
        0 1  ];
  H = [1 0];

  Q = [ 1 0;
        0 3 ];
  R = 10;

  x = [ 0 20 ]';
  P = 5*eye(2);
end


xp = A*x;  
Pp = A*P*A' + Q;    

K = 1 / (Pp(1,1) + R) * [Pp(1,1) Pp(2,1)]';  % Pp*H'*inv(H*Pp*H' + R);

x = xp + K*(z - H*xp);
P = Pp - K*H*Pp;   


pos = x(1);
vel = x(2);
```

**TestDeDvKalman.m**

```matlab
clear all


dt = 0.1;
t  = 0:dt:10;

Nsamples = length(t);

Xsaved   = zeros(Nsamples, 2);
DeXsaved = zeros(Nsamples, 2);
Zsaved   = zeros(Nsamples, 1);

for k=1:Nsamples
  z = GetPos();      
  [pos vel]   = DvKalman(z);
  [dpos dvel] = DeDvKalman(z);

  Xsaved(k, :)   = [pos vel];
  DeXsaved(k, :) = [dpos dvel];
  Zsaved(k)      = z;
end


figure
hold on
plot(t, Xsaved(:, 1), 'ro')
plot(t, DeXsaved(:,1))
xlabel('Time[sec]')
ylabel('Position[m]')
legend('Matrix','Decomposed','location','NorthWest')

figure
hold on
plot(t, Xsaved(:, 2), 'ro')
plot(t, DeXsaved(:,2))
xlabel('Time[sec]')
ylabel('Velocity[m/s]')
legend('Matrix','Decomposed','location','NorthWest')
```  


테스트 프로그램에 특별히 새로 추가된 코드는 없다. 두 칼만 필터 함수가 추정한 위치와
속도를 저장했다가 그래프로 그려 비교해 보는 게 전부이다.  

아래 그래프에 속도 추정 결과와 위치 추정 결과를 비교했다. 실선으로 표시한 궤적이
행렬식을 풀어 쓴 결과이고, 원으로 표시한 값은 행렬식으로 계산한 칼만 필터의 출력이다.
**두 값이 정확히 일치한다. 칼만 이득을 계산하는 두 개의 식이 수학적으로 동일하니 당연한
결과이다.**  

![DvKalman_7](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/DvKalman_7.PNG)  

![DvKalman_8](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/DvKalman_8.PNG)  

이 절에서는 칼만 이득의 행렬식을 풀어서 계산을 간단하게 하고 속도를 개선하는 기법을
살펴봤다. **그런데 이 방법은 상태변수가 많아서 시스템 모델의 행렬이 커지면 사용하기 어렵다.** 이 때는 효율적인 수치해석 라이브러리를 사다 쓰거나 만들어 쓰는 수 밖에 없다.  

## 11.7 시스템 모델의 힘  

칼만 필터는 잡음도 제거하지만 위치 정보만 가지고 속도를 추정해내는 능력도 있다는 사실을
알았다. 그런데 칼만 필터는 어떻게 측정하지도 않은 속도를 추정해낼까? 지금부터 이 놀라운
능력이 어디에서 나오는지 파헤쳐 보겠다.  

먼저 저주파 통과 필터를 생각해 보겠다. 저주파 통과 필터는 측정 신호에 대해 어떠한 가정도
하지않았다. 그저 새 측정값과 이전의 추정값에 가중치를 주고 더할 뿐이다. **따라서 측정하지
않은 물리량을 추정한다는 것은 애초부터 불가능한 이야기이다. 저주파 통과 필터에서는
측정값이 없으면 추정도 없다.**  

칼만 필터는 다르다. **시스템의 수학적 모델을 알고 있다고 가정한다. 즉 시스템이 어떤 법칙에 따라 측정값을 출력하는지를 정확히 알고 있다는 전제를 깔고 있다.** 측정하지도 않은
물리량을 정확히 추정해내는 비밀은 바로 여기에 있다. **상태변수 사이의 연관 관계를
나타낸는 시스템 모델을 통해, 측정하지 않은 상태변수를 간접적으로 추정해내는 것이다.**  

간단한 예를 들어 보겠다. 현재 속도가 $80m/s$ 이면 $1$ 초 뒤에는 $80m$ 만큼 이동해
있을 수 밖에 없다. 왜냐하면 속도를 적분하면 이동 거리가 되는 것은 불변의 물리법칙이기
때문이다. 측정 위치에 잡음이 섞여 있거나 시스템 모델에 오차가 다소 있다고 해도, 열차는
$80m$ 근처에 있어야지 $800m$ 위치에 있을 수는 없다. 반대의 경우도 마찬가지이다. $1$ 초
동안의 이동거리가 $80m$ 인데, 속도가 $800m/s$ 나 될 리는 없다. 대략 $80m/s$ 근방이어야
말이 된다. **이렇게 칼만 필터는 시스템 모델의 정보를 적극 활용해서 측정하지 않은
상태변수도 추정해낸다.** 예제의 칼만 필터는 바로 이 점을 이용해서 위치로 속도를 추정해
낸 것이다. 추정 속도도 시스템의 물리 법칙을 따라야 하니까, 자연히 위치 변화의 추이가 속도
추정값에 반영될 수 밖에 없다.  

**요약하면 칼만 필터가 측정하지 않은 물리량을 추정해내는 능력은 바로 '시스템 모델'
덕분이다.** 그런데 추정에 시스템 모델을 이용하는 것은 양날의 칼과 같다. 시스템 모델이
실제 시스템과 많이 다르면 추정 결과가 엉망이 될 뿐만 아니라, 심하면 칼만 필터
알고리즘이 발산해서 전체 시스템이 망가질 수도 있다.
