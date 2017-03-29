---
layout: post
title: "Kalman Filter Study _ 13장. 수평 자세 측정_1부"
date: 2017-03-28
categories: [kalman_filter, matlab]
---

지금까지 살펴본 칼만 필터의 용도는 잡음 제거와 측정하지 않은 상태변수 추정 두 가지였다.
이 장에서는 저렴한 센서를 모아서 '청출어람' 센서로 탈바꿈시키는 칼만 필터의 센서 융합
능력을 소개한다.  

칼만 필터가 최초로 적용된 곳은 항공우주 분야였다. 발표되자마자 미국의 아폴로 달 탐사
프로그램에 적용되면서 일약 스타로 떠올랐다. 가장 많은 응용사례가 있는 곳도 항공우주
분야이다. 특히 항공기나 인공위성의 위치와 자세를 측정하는 항법(`Navigation`) 분야에서는
그 영향력이 절대적이라고 할 만큼 많이 사용된다. 칼만 필터 없는 항법 시스템은 상상하기
어려울 정도이다. 이 장에서 다룰 예제도 가속도계(`Accelerometer`)와
자이로스코프(`Gyroscope`)로 수평 자세를 찾아내는 간단한 항법 문제이다.  

**참고로 가속도계나 자이로는 관성(`Inertial`) 좌표계에 대한 측정값을 출력한다고 해서
관성항법 센서라고 부른다. 그리고 관성항법 센서로 위치와 자세 등을 측정하는 항법을
관성항법이라고 한다.** 보통 관성항법은 자세뿐만 아니라 위치와 속도까지 다루는데,
여기서는 간단하게 자세 추정만 고려한다. 아래는 예제이다.  

**"헬기의 자세 측정 알고리즘을 개발해야 한다. 가속도계와 자이로를 이용해 헬기의 수평
자세를 알아내야 한다. 다시 말해 가속도와 각속도를 가지고, 수평면에 대해 헬기가 앞뒤와
좌우로 기울어진 각도를 찾아내야 한다."**  

비록 헬기를 예로 들었지만, 여기서 설명하는 내용은 보행 로봇 등 자이로와 가속도계로 수평
자세를 측정하는 모든 시스템에 적용 가능하다. 문제를 더 자세히 분석해보겠다. 일반적으로
어떤 물체의 자세는 아래 그림처럼 세 개의 각도만 알면 정확히 표현할 수 있다. 참고로 이
세 개의 각을 오일러 각(`Euler angle`)이라고 함!

![ARS_RYP](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/ARS_RYP.PNG)  

따라서 헬기의 자세를 알아낸다는 말은 이 세 각도를 찾는다는 말과 같은 뜻이다. 이 예제에서는
수평 자세에만 관심이 있으므로 세 개의 각도 중에서 롤각($\phi$)과 피치각($\theta$)만
찾으면 된다. 요각($\varphi$)은 수평 자세에서는 전혀 영향을 주지 않으므로 고려할 필요가
없다.  

예제 시험 절차는 다음과 같다. 주파수 $0.2 Hz$, 최대 진폭 $\pm{30^\circ}$인 정현파로
항법센서를 흔드는 시험을 실시했다.  

![ARS_sub1](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/ARS_sub1.PNG)  

시험에서 저장한 데이터는 세 축 방향 가속도와 각속도이고, 저장 간격은 $0.01$ 초였다. 다음
그림은 항법센서에 가한 롤-피치-요축 정현파 진동의 궤적이다.  

![ARS_1](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/ARS_1.PNG)  

이 장에서 개발하는 알고리즘의 검증과 분석에는 위의 시험에서 얻은 가속도와 각속도를 공통으로
사용한다. 알고리즘의 성능이 좋은지 나쁜지는 추정 결과가 앞의 진동 궤적과 얼마나 비슷한
지로 판단하면 된다.  

이어지는 절에서는 자이로와 가속도계로 수평 자세를 계산하는 방법을 소개한다. 그리고 측정
결과를 분석해서 두 센서가 왜 상호 보완이 되는지를 설명하겠다. 마지막으로 칼만 필터로 두
센서를 융합해서 수평 자세를 찾아내는 기법을 살펴보고, 융합이 성공적으로 되었는지 검토해
보겠다.  

## 13.1 자이로를 이용한 자세 결정

**자이로로 측정한 각속도를 적분해서는 오일러각을 구할 수 없다. 자이로는 오일러각의
변화율($\dot{\phi},\dot{\theta},\dot{\varphi}$)이 아니라, 헬기의
각속도($p,q,r$)를 측정한 값이기 때문이다.** 따라서 자이로의 측정값을 오일러각의
변화율로 바꿔서 적분해야 한다. 동역학 분야에서 오일러각과 각속도의 관계는 이미 잘
알려져 있다. **참고로 오일러각의 변화율을 적분하여 오일러각을 구하고, 이전 오일러각과
적분한 값을 더하여 현재의 오일러각을 구할 수 있다.**  

$$
\begin{bmatrix} \dot{\phi} \\\\ \dot{\theta} \\\\ \dot{\varphi} \end{bmatrix} = \begin{bmatrix} 1 & \sin{\phi}\tan{\theta} & \cos{\phi}\tan{\theta} \\\\ 0 & cos{\phi} & -\sin{\phi} \\\\ 0 & \sin{\phi}/\cos{\theta} & \cos{\phi}/\cos{\theta} \end{bmatrix} \begin{bmatrix} p \\\\ q \\\\ r \end{bmatrix} \label{13.1}\tag{13.1}
$$  

이 식에 자이로의 측정 각속도($p,q,r$)를 대입해서 적분하면 현재 자세를 구할 수 있다. 물론
초기값은 알고 있어야 한다. **그런데 자이로의 측정값에는 오차가 섞여 있기 때문에 적분하면
자세에는 오차가 누적될 수 밖에 없다. 따라서 이 방법은 센서가 아주 정밀하거나 운용 시간이
짧지 않으면 실제로 사용하기는 어렵다.**  

아래 `EulerGyro.m`은 식 $\ref{13.1}$을 구현한 함수의 코드이다. 자이로에서 측정한
각속도와 샘플링 시간을 인자로 받아 오일러각을 반환한다. 적분은 가장 단순한 알고리즘을
사용했다. 실전에서는 문제의 특성에 따라 적절한 적분 방법으로 대체하면 된다.  

**EulerGyro.m**

```matlab
function [phi theta psi] = EulerGyro(p, q, r, dt)
% 자이로에서 측정한 각속도를 오일러각의 변화율로 바꾸고 적분하는 함수
% 자이로에서 측정한 각속도(p,q,r)와 샘플링 시간(dt)를 인자로 받음

persistent prevPhi prevTheta prevPsi

% 초기 오일러각은 모두 0으로 가정
if isempty(prevPhi)
    prevPhi = 0;
    prevTheta = 0;
    prevPsi = 0;
end

% 사용할 값으로 변환
sinPhi = sin(prevPhi);
cosPhi = cos(prevPhi);
cosTheta = cos(prevTheta);
tanTheta = tan(prevTheta);

% 적분(문제의 특성에 따라 적절히 변경)
phi   = prevPhi     + dt*(p + q*sinPhi*tanTheta + r*cosPhi*tanTheta);
theta = prevTheta   + dt*(    q*cosPhi          - r*sinPhi);
psi   = prevPsi     + dt*(    q*sinPhi/cosTheta + r*cosPhi/cosTheta);

prevPhi = phi;
prevTheta = theta;
prevPsi = psi;

end
```


아래 `TestEulerGyro.m`은 `EulerGyro` 함수를 테스트하기 위한 프로그램이다. 초기
오일러각은 모두 $0$이라고 가정했다.  

**TestEulerGyro.m**

```matlab
clear all

Nsamples = 41500;
EulerSaved = zeros(Nsamples, 3);
GetGyroSaved = zeros(Nsamples, 3);

dt = 0.01;

for k = 1:Nsamples
  [p q r] = GetGyro();   
  [phi theta psi] = EulerGyro(p, q, r, dt);

  GetGyroSaved(k, :) = [ p q r ];
  EulerSaved(k, :) = [ phi theta psi ];
end

% radian -> degree
PhiSaved   = EulerSaved(:, 1) * 180/pi;
ThetaSaved = EulerSaved(:, 2) * 180/pi;
PsiSaved   = EulerSaved(:, 3) * 180/pi;

t = 0:dt:Nsamples*dt-dt;

% 자이로센서가 측정한 각속도 확인 - 교재 그림과 맞춰주려고 40을 곱함
figure
subplot(3,1,1);
plot(t, GetGyroSaved(:,1)*40);
xlabel('Time[sec]')
ylabel('Roll angle[deg]')
subplot(3,1,2);
plot(t, GetGyroSaved(:,2)*40);
xlabel('Time[sec]')
ylabel('Pitch angle[deg]')
subplot(3,1,3);
plot(t, GetGyroSaved(:,3)*40);
xlabel('Time[sec]')
ylabel('Yaw angle[deg]')


% 오일러 각으로 변환한(각속도를 변화율로 변화 + 적분) 값 확인
figure
plot(t, PhiSaved)
xlabel('Time[sec]')
ylabel('Roll angle[deg]')

figure
plot(t, ThetaSaved)
xlabel('Time[sec]')
ylabel('Pitch angle[deg]')

% Yaw각은 수평자세에는 전혀 영향을 주지 않으므로 고려할 필요가 없다.(지금 예제)
figure
plot(t, PsiSaved)
xlabel('Time[sec]')
ylabel('Yaw angle[deg]')
```


아래 `GetGyro` 함수는 자이로에서 측정값을 읽어온다. 여기서는 미리 `ArsGyro.mat` 파일에
저장된 값을 반환하도록 했다.  

**GetGyro.m**

```matlab
function [p q r] = GetGyro()
% 자이로에서 측정값을 읽어오는 함수
% 미리 저장된 ArsGyro.mat에서 값 읽어옴
persistent wx wy wz
persistent k firstRun


if isempty(firstRun)
  load ArsGyro
  k = 1;

  firstRun = 1;
end

p = wx(k);
q = wy(k);
r = wz(k);

k = k + 1;
```


다음 그래프는 앞 절의 시험에서 자이로가 실제로 측정한 각속도이다. 실제 기동의 경향이 잘
나타나 있다. 실험에 쓰인 항법 센서는 잡음 제거 필터가 구현된 제품이라, 저가의 자이로보다
출력이 더 깨끗한 편이다.  

![ARS_1](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/ARS_1.PNG)  

그럼 `TestEulerGyro` 프로그램의 수행 결과를 살펴보겠다. 먼저 롤각($\phi$)을 보겠다.
$50 \sim 130$ 초 사이에 $\pm{30\circ}$ 의 진폭으로 진동하는 움직임이 보인다.
$180 \sim 250$ 초 사이에도 비슷한 움직임이 다시 나타난다. 외부에서 가한 진동이 잘 반영된
결과이다. 그런데 시간이 지나면서 오차가 조금씩 생기는게 보인다. **기동의 경향은 어느 정도
맞지만, 자세각은 많이 편향되어 있다. 오차가 누적된 탓이다.**  

![ARS_2](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/ARS_2.PNG)  

이제 피치각($\theta$)의 변화를 살펴보겠다. $0 \sin 180$ 초 사이에는 피치축 기동이 없는데도
측정값이 점점 커져간다. **롤축 기동의 영향을 받아 오차가 계속 쌓여가기 때문이다. 이렇게
누적된 측정 오차가 상당하다.** 그렇다고 적분한 자세각이 아무런 쓸모도 없는 것은 아니다.
**오차 누적으로 값은 편향되어 가지만, 기동 패턴은 잘 감지하고 있기 때문이다.**  

**요약하면 각속도를 적분하여 얻은 자세는 동적 움직임은 잘 포착해내지만, 점차 오차가
누적되어 실제값에서 멀어지는 특성이 있다. 따라서 자이로는 자세각 측정보다는 자세각의
동태를 측정하는데 사용하는게 더 낫다.** 이러한 특징을 시간의 관점에서 보면, 자이로는
단기간의 측정은 비교적 정확하지만 장시간의 변화에는 부정확한 센서라고 볼 수 있다.  

![ARS_3](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/ARS_3.PNG)  

![ARS_4](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/ARS_4.PNG)  

## 13.2 가속도를 이용한 자세 결정

**가속도계로 측정한 가속도($f_x,f_y,f_z$)에는 중력 가속도와 속도의 크기나 방향이 바뀔 때
생기는 가속도 등 다양한 종류의 가속도가 포함되어 있다.** 이런 특성을 수식으로 표현하면
다음과 같다.  

$$
\begin{bmatrix} f_x \\\\ f_y \\\\ f_z \end{bmatrix} = \begin{bmatrix} \dot{u} \\\\ \dot{v} \\\\ \dot{w} \end{bmatrix} + \begin{bmatrix} 0 & w & -v \\\\ -w & 0 & u \\\\ v & -u & 0 \end{bmatrix} \begin{bmatrix} p \\\\ q \\\\ r \end{bmatrix} + g \begin{bmatrix} \sin{\theta} \\\\ -\cos{\theta}\sin{\phi} \\\\ -\cos{\theta}\cos{\phi}\end{bmatrix} \label{13.2}\tag{13.2}
$$  

여기서 $u,v,w$는 이동 속도를 의미하고, $p,q,r$은 회전 각속도를 뜻한다. 그리고 $g$는
중력 가속도를 나타낸다.  

식 $\ref{13.2}$의 우변에서 마지막 항을 보면 롤각($\phi$)과 피치각($\theta$)이 나온다.
**따라서 나머지 항의 값을 모두 알면, 이 식으로 수평 자세를 계산할 수 있다.** 그럼 하나씩
따져보겠다. 가속도($f_x,f_y,f_z$)와 각속도($p,q,r$)는 측정값이라 알고 있다. 중력
가속도($g$)도 이미 알고 있는 값이다. 이제 남은 것은 이동 속도($u,v,w$)와 이동
가속도($\dot{u},\dot{v},\dot{w}$)뿐이다. 그런데 이 두 값은 아주 고가의 항법센서가
아니면 측정할 수 없다. 따라서 평범한 항법센서로는 식 $\ref{13.2}$를 이용해 자세를
계산할 방법이 없다.  

그렇다고 아주 불가능한 것은 아니다. **시스템이 정지해 있거나 일정한 속도로 직진하는
조건이라면 가능하다.** 먼저 시스템이 정지해 있을 때를 생각해보겠다. 움직이지 않으면
이동 속도와 이동 가속도 모두 $0$이 된다.  

$$
\dot{u} = \dot{v} = \dot{w} = 0 \\\\
u = v = w = 0
$$  

일정한 속도로 직진하는 경우를 보겠다. 일단 이동 가속도($\dot{u}, \dot{v}, \dot{w}$)는
$0$ 이다. 그리고 자세 변화가 없으니 각속도($p,q,r$)도 $0$이 된다.  

$$
\dot{u} = \dot{v} = \dot{w} = 0 \\\\
p = q = r = 0
$$

두 경우 모두 식 $\ref{13.2}$의 우변에 있는 첫 번째 항과 두 번째 항이 $0$이 된다. 그러면
식 $\ref{13.2}$는 다음과 같이 간단한 형태가 된다.  

$$
\begin{bmatrix} f_x \\\\ f_y \\\\ f_z \end{bmatrix} = g \begin{bmatrix} \sin{\theta} \\\\ -\cos{\theta}\sin{\phi} \\\\ -\cos{\theta}\cos{\phi}\end{bmatrix}
$$

이 식에서 다음과 같은 롤각과 피치각의 공식을 유도해낼 수 있다.  

$$
\phi = \sin^{-1}{\left(-f_y \over g\cos\theta \right)} \\\\
\theta = \sin^{-1}{\left( f_x \over g \right)} \label{13.4}\tag{13.4}
$$  

**움직이는 속도가 충분히 느리거나 속도의 크기와 방향이 빠르게 변하지 않는 경우에도 식
$\ref{13.4}$로 수평자세를 구할 수 있다. 이 경우에 식 $\ref{13.4}$는 근사식이 된다.**
보통 헬기는 대부분의 임무를 제자리 비행이나 일정 속도로 나는 도중에 수행하므로, 이런
가정이 잘 맞는다. 보행 로봇도 대부분 비슷한 조건에서 움직이므로 식 $\ref{13.4}$를 수평
자세에 이용해도 무방하다.  

**식 $\ref{13.4}$로 수평 자세를 구할 때는 이 식이 근사식이라는 사실을 꼭 기억해야 한다.
빠른 속도로 회전하거나 속도 변화가 심하면 오차가 커질 수 있다.**  

아래 `EulerAccel.m`은 식 $\ref{13.4}$를 구현한 것이다. 가속도를 입력 받아 롤각과
피치각을 반환한다.  

**EulerAccel.m**

```matlab
function [ phi theta ] = EulerAccel(ax, ay)
% 가속도를 입력 받아 롤각과 피치각(오일러각)을 반환하는 함수
%

% 중력 가속도
g = 9.8;

theta = asin(  ax/ g );
phi   = asin( -ay/ (g*cos(theta)) );

end
```


다음 그래프는 앞절의 시험에서 측정한 가속도이다. 그림을 보면 롤축 기동을 할 때 피치축
가속도가 측정되고, 피치축이 움직일 때 롤축 가속도가 나타난다. 이 현상의 이유는 이렇다.
**롤축 기동은 롤축을 회전축으로 하는 주기적인 정현파 진동이므로, 피치축이 수평면에 대해
올라가거나 내려간다. 그러면 중력가속도와 원심력 성분 등이 피치축 가속도계에 측정된다.**
피치축 기동일 때도 마찬가지이다. 따라서 아래와 같은 결과는 물리 법칙에 위배되지 않는,
타당한 결과이다.  

![ARS_5](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/ARS_5.PNG)  

`TestEulerAccel.m`은 `EulerAccel` 함수를 테스트하기 위한 프로그램이다.  

**TestEulerAccel.m**

```matlab
clear all

Nsamples = 41500;
EulerSaved = zeros(Nsamples, 2);
GetAccelSaved = zeros(Nsamples, 3);

for k = 1:Nsamples
  [ax ay az] = GetAccel();   
  [phi theta] = EulerAccel(ax, ay);

  GetAccelSaved(k, :) = [ax ay az];
  EulerSaved(k, :) = [phi theta];
end

% radian -> degree
PhiSaved   = EulerSaved(:, 1) * 180/pi;
ThetaSaved = EulerSaved(:, 2) * 180/pi;

dt = 0.01;
t  = 0:dt:Nsamples*dt-dt;

% 가속도센서가 측정한 가속도 확인
figure
subplot(3,1,1);
plot(t, GetAccelSaved(:, 1));
xlabel('Time[sec]')
ylabel('f_x[m/s^2]')
subplot(3,1,2);
plot(t, GetAccelSaved(:, 2));
xlabel('Time[sec]')
ylabel('f_y[m/s^2]')
subplot(3,1,3);
plot(t, GetAccelSaved(:, 3));
xlabel('Time[sec]')
ylabel('f_z[m/s^2]')

% 변환한 롤각과 피치각 확인
figure
plot(t, PhiSaved)
xlabel('Time[sec]')
ylabel('Roll angle[deg]')

figure
plot(t, ThetaSaved)
xlabel('Time[sec]')
ylabel('Pitch angle[deg]')
```


`GetAccel` 함수는 가속도를 측정해서 읽어온다. 미리 3축 방향 가속도가 저장된
파일(`ArsAccel.mat`)을 읽어 들인 다음, 차례로 데이터를 반환한다.  

**GetAccel.m**

```matlab
function [ax ay az] = GetAccel()
% 가속도를 측정해서 읽어오는 함수
% 미리 저장된 ArsAccel.mat에서 값 읽어옴
persistent fx fy fz
persistent k firstRun


if isempty(firstRun)
  load ArsAccel
  k = 1;

  firstRun = 1;
end

ax = fx(k);
ay = fy(k);
az = fz(k);

k = k + 1;
```


가속도로 계산한 수평 자세를 살펴보겠다. 먼저 롤각 그래프를 보면 정현파 기동의 추이가 잘
나타난다. 그리고 자이로와 달리 기동이 끝나면 다시 $0$도로 정확히 돌아온다. 오차가
누적되지 않는다. 그런데 최대값은 $\pm9^\circ$ 정도로 실제 값인 $\pm30^\circ$에 미치지
못한다. 오차가 상당히 커서 가속도계 단독으로 사용하기는 힘든 상태이다.  

![ARS_6](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/ARS_6.PNG)  

이제 피치각의 그래프를 보겠다. 피치각의 그래프에서도 같은 양상이 보인다. 시간이 지나도
누적 오차가 없고 피치각이 편향되지도 않는다. 하지만 롤각과 마찬가지로 오차가 너무 크다.  

**정리하면 가속도계는 오차를 발산하지 않고 일정한 범위 안에 머무르는 장점이 있다. 자세를
구하는 과정에서 적분을 하지 않기 때문이다. 하지만 안정성이 높은 대신 정밀도는 떨어진다.**
왜냐면 식 $\ref{13.4}$의 자세 계산식이 가속도나 각속도가 충분히 작은 상황에서만 쓸 수
있는 근사식이기 때문이다.

![ARS_7](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/ARS_7.PNG)  
