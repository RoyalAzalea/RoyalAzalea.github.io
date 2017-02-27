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
뭔지 알아보자. 먼저 식 $2.1$의 이동평균 정의를 별도의 항으로 풀어 보자.  

$$
\begin{align}
\overline{x_k} &= {x_{k-n+1} + x_{k-n+2} + \cdots + x_k \over n} \\\\
               &= {1 \over n}x_{k-n+1} + {1 \over n}x_{k-n+2} + \cdots + {1 \over n}x_k
\end{align}
$$

이 식을 보면 이동평균은 모든 데이터에 동일한 가중치($1/n$)를 부여한다. 가장 최근의
데이터($x_k$)와 가장 오래된 데이터($x_{k-n+1}$)를 같은 비중으로 평균에 반영한다.
이게 성능과 무슨 관련이 있을까? 다음과 같은 경우를 생각해보자.  

어떤 항공기에 장착된 자세 센서틑 $1$초에 $50$번씩 측정값을 출력한다. 자동비행 컴퓨터의
이동평균 필터는 $50$개의 데이터를 모아서($n=50$) 평균을 계산한다. 이 경우 이동평균
계산에 동원되는 측정값에서 가장 오래된 데이터는 $1$초 전에 측정한 값이다. 항공기가 계속
비행하고 있는 상황에서 $1$초 전에 측정값과 최신 측정값 중 어느 쪽이 더 현재 자세에
가까울까? 최신 측정값일 가능성이 더 높다. 그런데 이동평균은 모든 측정값에 동일한
가중치를 주고 처리한다.  

**이러한 이동평균의 약점은 측정값의 변화가 클수록 더 두드러진다. 이처럼 이동평균 필터는
변화가 심한 신호에 적용하면 잡음 제거와 변화 민감성을 동시에 달성하기 어렵다. 그렇다고
이동평균 필터가 쓸모 없지는 않으니, 사용하는 필터의 장점과 한게를 정확히 알고 적재적소에
적용하는 것이 중요하다.**  

이제 저주파 통과 필터 보겠다. 저주파 통과 필터는 이동평균 필터의 단점을 보완했다.
이 필터의 정확한 이름은 **`1차 저주파 통과 필터`** 이다.  

$$
\overline{x_k} = \alpha\overline{x_{k-1}} + (1-\alpha)x_k \label{3.1}\tag{3.1}
$$

여기서 $\alpha$는 $0 < \alpha < 1$인 상수이다. 이 식은 식 $1.4$의 평균 필터와 비슷하다.
비슷하지만 평균 필터에서는 $\alpha$를 임의로 지정할 수 없고, 데이터 개수에 따라 자동으로
결정된다는 점이 다르다. **그런데 저주파 통과 필터의 $\overline{x_k}$는 평균과는 직접 관련
없는 값이다. 따라서 지금부터는 평균이라는 단어 대신 추정값(`Estimated value`)라는 용어를
사용하겠다.**  

그럼 식 $\ref{3.1}$이 이동평균 필터의 단점을 보완한 필터인지 보겠다. 식 $\ref{3.1}$에서
직전 추정값 $\overline{x_{k-1}}$는 다음과 같이 계산된다.  

$$
\overline{x_{k-1}} = \alpha\overline{x_{k-2}} + (1-\alpha)x_{k-1} \label{3.2}\tag{3.2}
$$

이 식을 식 $\ref{3.1}$에 대입해서 정리해 보면 다음과 같은 관계식이 나온다.  

$$
\begin{align}
\overline{x_k} &= \alpha\overline{x_{k-1}} + (1-\alpha)x_k \\\\
               &= \alpha\left\{ \alpha\overline{x_{k-2}} + (1-\alpha)x_{k-1} \right\} + (1-\alpha)x_k \\\\
               &= \alpha^2\overline{x_{k-2}} + \alpha(1-\alpha)x_{k-1} + (1-\alpha)x_k \label{3.3}\tag{3.3}
\end{align}
$$

여기서 측정 데이터($ x_{k-1}, x_k $)에 곱해진 계수 $\alpha(1-\alpha)$와 $1-\alpha$의
크기를 비교해 보겠다. $\alpha$가 $\alpha$는 $0 < \alpha < 1$인 상수이므로 두 계수
사이에는 다음과 같은 관계가 성립한다.  

$$
\alpha(1-\alpha) < 1-\alpha
$$

이 부등식과 식 $\ref{3.3}$을 비교해보면 최근 측정값($x_k$)이 이전 측정값($x_{k-1}$)보다
더 큰 가중치로 추정값에 반영되는 것을 알 수 있다. 더 확실히 알아보겠다. 식 $\ref{3.1}$을
참고하면 $\overline{x_{k-2}}$는 다음과 같이 정의된다.  

$$
\overline{x_{k-2}} = \alpha\overline{x_{k-3}} + (1-\alpha)x_{k-2} \label{3.4}\tag{3.4}
$$

이 식을 식 $\ref{3.3}$에 대입해서 정리하면 아래와 같다.  

$$
\begin{align}
\overline{x_k} &= \alpha^2\overline{x_{k-2}} + \alpha(1-\alpha)x_{k-1} + (1-\alpha)x_k \\\\
               &= \alpha^2\left\{ \alpha\overline{x_{k-3}} + (1-\alpha)x_{k-2} \right\} + \alpha(1-\alpha)x_{k-1} + (1-\alpha)x_k \\\\
               &= \alpha^3\overline{x_{k-3}} + \alpha^2(1-\alpha)x_{k-2} + \alpha(1-\alpha)x_{k-1} + (1-\alpha)x_k \label{3.5}\tag{3.5}
\end{align}
$$

이제 $\alpha$의 지수가 더 분명하게 보인다. $\alpha$는 $1$보다 작은 상수이므로 $\alpha$
는 지수가 커질수록 값이 기하급수적으로 작아진다. 따라서 측정 데이터의 계수 사이에는
다음과 같은 관계가 성립한다.  

$$
\alpha^2(1-\alpha) < \alpha(1-\alpha) < 1-\alpha
$$

식 $\ref{3.5}$와 이 부등식을 통해 이전 측정 데이터일수록 더 작은 계수가 곱해진다는
사실을 다시 한번 확인했다. 즉 이전 측정값일수록 추정값 계산에 반영되는 비중이 작다.  

**위 과정을 통해 저주파 통과 필터는 오래된 측정값일수록 더 작은 가중치를 부여하는 필터라는
사실이 분명해졌다. 이러한 가중치 차별화 덕분에 저주파 통과 필터는 잡음제거와 변화
민감성이라는 상충되는 요구를 이동평균 필터보다 더 잘 충족한다.**  

그리고 식 $\ref{3.1}$의 1차 저주파 통과 필터를 "지수가중(`Exponentially weighted`)
이동평균 필터" 라고도 부른다. 그 이유는 오래된 측정값일수록 기하급수적으로 가중치를
낮게 주어 이동평균을 계산한다고 해서 이런 이름이 붙었다.  

이제 저주파 통과 필터 함수를 MATLAB으로 구현하겠다. 이 함수는 측정데이터를 받아 추정값을
반환한다. 가중치를 조절하는 $\alpha$를 상수로 지정했는데, 실행 중에 바꿔야 한다면 이
값도 인자로 받도록 수정하면 된다. 그리고 추정값이 초기에 측정값과 너무 동떨어지지 않도록,
직전 추정값(`pervX`)을 $0$대신 첫 번째 측정 데이터로 초기화했다.  

**LPF.m**

```matlab
function [ xlpf ] = LPF(x)
% 저주파 통과 필터 함수
%   재귀적 구현
% 평균값이 아닌 추정값 사용

persistent prevX
persistent firstRun

if isempty(firstRun)
    % 추정값이 초기의 측정값과 너무 동떨어지지 않도록, 0 대신 첫 번째 측정 데이터로 초기화함
    prevX = x;

    firstRun = 1;
end

% 가중치 설정
alpha = 0.7;
xlpf = alpha*prevX + (1 - alpha)*x;

prevX = xlpf;

end
```


다음은 저주파 통과 필터를 테스트하는 예제이다.  

**TestLPF.m**

```matlab
clear all

Nsamples = 500;
Xsaved = zeros(Nsamples, 1);
Xmsaved = zeros(Nsamples, 1);

for k=1:Nsamples
    xm = GetSonar();
    x  = LPF(xm);

    Xsaved(k) = x;
    Xmsaved(k) = xm;
end

dt = 0.02;
t = 0:dt:Nsamples*dt-dt;

figure
hold on
plot(t, Xmsaved, 'r.');
plot(t, Xsaved, 'b');
xlabel('Time[sec]');
ylabel('Altitude[m]');
text(1,100,'\alpha = 0.7');
legend('Measured','LPF','location','NorthWest')
```


위 `TestLPF.m`에서 쓰인 `GetSonar`는 2장에서 쓰인 것과 동일하다. 즉, 이 예제와 2장의
이동평균 필터에서 사용하는 측정 데이터는 동일하다.  

아래는 실행 결과이다. 그래프를 보면 측정 데이터의 잡음이 잘 제거되고, 고도 변화 추이도
잘 나타나 있다. 시간 지연도 이동평균 필터보다는 개선되었다. 그렇다고 저주파 통과 필터가
이동평균 필터보다 항상 시간 지연이 적은 것은 아니다. 측정 데이터의 특성에 맞게 $\alpha$를
선정했는지가 중요하다. 1차 저주파 통과 필터의 $\alpha$는 이동평균 필터의 데이터
개수($n$)와 비슷한 역할을 한다. 이 값으로 잡음 제거와 변화 추종 성능을 절충할 수 있다.  

![LPF_1](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/LPF_1.PNG)  

아래는 $\alpha$를 각기 달리 설정한 실행 결과이다. **$\alpha$의 값이 작으면 잡음이 더
많고, $\alpha$의 값이 크면 잡음은 줄어든 대신 시간지연이 더 커진다.**  

그 이유를 보도록하자. 식 $\ref{3.1}$에서 $\alpha$가 작으면 $1-\alpha$ 값은 상대적으로
더 커진다. 따라서 추정값에 측정값이 더 많이 반영된다. 이렇게 되면 저주파 통과 필터는 잡음
제거보다는 측정값의 변화에 더 민감해진다. 그래서 $\alpha$가 작을수록 추정값에 잡음이
더 많이 나타난다.  반대로 $\alpha$가 커지면 직전 추정값의 비중이 더 커진다. 그러면
추정값이 직전 추정값에서 별로 달라지지 않는다. 결국 $\alpha$가 크면 잡음이 줄어들고
추정값 그래프의 변화가 무뎌진다.  

![LPF_2](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/LPF_2.PNG)  

정리하면 1차 저주파 통과 필터는 이동평균 필터와 달리 최신 측정값일수록 가중치를 높게 주는
좋은 특성을 가졌다. 이 덕분에 측정 신호의 변화 추이를 이동평균 필터보다 더 잘 감지해낸다.
