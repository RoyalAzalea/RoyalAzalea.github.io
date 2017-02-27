---
layout: post
title: "Kalman Filter Study _ 2장. 이동평균 필터"
date: 2017-02-22
categories: [kalman_filter, matlab]
---

평균을 내면 측정 데이터에서 잡음을 제거할 수 있다. 하지만 측정하려는 물리량이 시간에
따라 변하면 평균은 적절하지 않다. 평균은 데이터의 동적인 변화는 모두 없애버리고, 측정
데이터를 뭉뚱그려 하나의 값만 내놓기 때문이다.  

우리가 다루는 물리량은 대부분 시간이 지남에 따라 변화한다. 잡음을 없애는 동시에 시스템의
동적인 변화를 제대로 반영하려면 **이동평균**(`Moving Average`)을 사용해야 한다.  

이동평균은 모든 측정 데이터가 아니라, 지정된 개수의 최근 측정값만 가지고 계산한 평균이다.
새로운 데이터가 들어오면 가장 오래된 데이터는 버리는 방식으로, 데이터 개수를 일정하게
유지하면서 평균을 구한다. 참고로 이름에 **이동**(`Moving`)이라는 단어가 붙은 것도 이
때문이다.  

이제 $n$개의 데이터에 대한 이동평균을 수식으로 표현하면 다음과 같다.

$$
\overline{x_k} = { x_{k-n+1} + x_{k-n+2} + \cdots + x_k \over n} \label{2.1}\tag{2.1}
$$


**여기서 이동평균의 $\overline{x_k}$는 평균 필터의 $\overline{x_k}$와는 의미가 다르다.
평균 필터에서는 $k$개 데이터의 평균이라는 의미였지만, 이동평균 필터에서는 $k-n+1$번째
데이터부터 $k$번째 데이터까지 총 $n$개 데이터의 평균을 의미한다.**  

위 식 $\ref{2.1}$은 배치식이다. 이 식을 재귀식으로 바꿔보자. 직전의 이동평균
$\overline{x_{k-1}}$이 필요하다.  

$$
\overline{x_{k-1}} = { x_{k-n} + x_{k-n+1} + \cdots + x_{k-1} \over n} \label{2.2}\tag{2.2}
$$

위 두 식을 비교해보면 $\overline{x_k}$는 $\overline{x_{k-1}}$에서 가장 오래된
$x_{k-n}$을 뺀 대신 $x_k$를 새로 추가해서 구한 평균이라는 것을 알 수 있다.  

이제 식 $\ref{2.1}$에서 식 $\ref{2.2}$를 빼서 정리해보자.  

$$
\begin{align}
\overline{x_k} - \overline{x_{k-1}} &= { x_{k-n+1} + x_{k-n+2} + \cdots + x_k \over n} - { x_{k-n} + x_{k-n+1} + \cdots + x_{k-1} \over n} \\\\
                                    &= {x_k - x_{k-n} \over n}
\end{align}
$$

이 식을 $\overline{x_k}$에 대해 정리하면 다음과 같은 재귀식이 나온다.  

$$
\overline{x_k} = \overline{x_{k-1}} + {x_k - x_{k-n} \over n} \label{2.3}\tag{2.3}
$$

이 식이 바로 **이동평균 필터** 이다. 근데 이동평균 필터의 경우 재귀식이 배치식보다
사용하는데 이점이 없다. 둘 다 총합을 구하는데 필요한 데이터 개수는 $n$개로 일정해
재귀식을 사용한다고 계산량에 이점이 있는것도 아니다. 또 메모리 저장공간이 절약되는
이점도 없다. 즉, 이동평균 필터의 경우 재귀식이나 배치식이나 성능이 비슷하다.  

이제 이동평균 필터를 MATLAB 함수로 구현하겠다. `MovAvgFilter.m`은 재귀식으로 구현하였고,
`MovAvgFilter2.m`는 배치식으로 구현하였다.  

**MovAvgFilter**

```matlab
function [ avg ] = MovAvgFilter(x)
% 이동 평균 함수
%   재귀적 구현

persistent prevAvg n xbuf
persistent firstRun

if isempty(firstRun)
    % 데이터 개수 설정
    n = 10;
    % 처음 입력되는 측정 데이터로 내부 버퍼를 초기화
    xbuf = x*ones(n+1,1);

    prevAvg = x;
    firstRun = 1;
end

for m=1:n
    xbuf(m) = xbuf(m+1);
end
xbuf(n+1) = x;

avg = prevAvg + (x - xbuf(1)) / n;

prevAvg = avg;

end
```

**MovAvgFilter2**

```matlab
function [ avg ] = MovAvgFilter2(x)
% 이동 평균 함수
%   배치적 구현

persistent n xbuf
persistent firstRun

if isempty(firstRun)
    % 데이터 개수 설정
    n = 10;
    % 처음 입력되는 측정 데이터로 내부 버퍼를 초기화
    xbuf = x*ones(n, 1);

    firstRun = 1;
end

for m=1:n-1
    xbuf(m) = xbuf(m+1);
end
xbuf(n) = x;

avg = sum(xbuf) / n;

end
```


재귀식의 경우 최신 데이터 $x_k$에서 가장 오래된 데이터 $x_{k-n}$을 빼야하므로 설정한
$n$개 보다 하나 더 많은 $n+1$개의 데이터를 가지고 있어야 한다. 그리고 프로그램 내부
버퍼(`xbuf`)의 값을 처음 입력되는 측정 데이터로 설정하면 $0$으로 초기화하는 것보다
초기 오차를 줄일 수 있다.  

다음은 이동평균 필터를 테스트하는 예제이다.

**TestMovAvgFilter.m**

```matlab
clear all

Nsamples = 500;
Xsaved = zeros(Nsamples, 1);
Xmsaved = zeros(Nsamples, 1);

for k=1:Nsamples
  xm = GetSonar();
  x  = MovAvgFilter(xm);

  Xsaved(k)  = x;
  Xmsaved(k) = xm;
end


% 이동 평균 테스트
% 0.02초 간격으로 초음파측정
dt = 0.02;
t = 0:dt:Nsamples*dt-dt;

figure
hold on
plot(t, Xmsaved, 'r.');
plot(t, Xsaved, 'b');
xlabel('Time[sec]');
ylabel('Altitude[m]');
text(1,100,'n=10');
legend('Measured','LPF','location','NorthWest')
```

**GetSonar.m**

```matlab
function [ h ] = GetSonar()
% 초음파 거리계의 측정값을 읽어오는 함수
%   SonarAlt.mat 파일에 저장된 임시 데이터 사용

persistent sonarAlt     % SonarAlt.mat
persistent k firstRun

if isempty(firstRun)
    load SonarAlt
    k = 1;

    firstRun = 1;
end

h = sonarAlt(k);

k = k + 1;

end
```


위 `GetSonar.m`는 초음파 거리계의 측정값을 읽어오는 함수이다. 측정 데이터는 미리 준비된
`SonarAlt.mat` 파일에서 가져온다. 아래는 실행 결과이다.  

![MovAvgFilter_1](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/MovAvgFilter_1.PNG)  

위 그래프을 보면 이동평균 처리된 고도에 약간씩 시간지연이 있다. 실제 고도의 변화가 바로
반영되지 않고 조금씩 늦게 나타나고 있다. 만약 시간지연이 너무 크다면 데이터 개수를
줄여야한다. 대신 측정 잡음을 제거하는 성능은 더 떨어지게 된다. 반대로 데이터 개수를
늘리면 잡음 제거 성능은 개선되지만 시간지연은 커진다. 아래는 데이터 개수를 변화시킨
실행 결과이다. 아래는 데이터 개수를 변경시킨 실행 결과이다.  

![MovAvgFilter_2](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/MovAvgFilter_2.PNG)  

![MovAvgFilter_3](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/MovAvgFilter_3.PNG)  

위 그래프들을 보면 데이터 개수에 따른 잡음 제거와 시간지연의 변화를 볼 수 있다. 데이터
개수인 $n$은 잡음 제거와 변화 민감성이라는 상충된 요구를 절충하는 역할을 한다. 만약
측정하는 물리량이 빠르게 변한다면 이동평균의 데이터 개수를 줄여 변화를 빨리 쫓아가야 한다.
움직임이 느리다면 이동평균의 데이터 개수를 늘려 잡음 제거 특성을 높이는게 좋다.  

정리하면 이동평균 필터는 측정 데이터의 잡음을 제거하는데 유용하다. **평균 내는 데이터 개수가
많으면 잡음 제거 성능은 좋지만 측정 신호의 변화가 제 때 반영되지 않고 시간지연이 생긴다.
반대로 데이터 개수가 적으면 측정 신호의 변화는 잘 따라가지만 잡음이 잘 제거되지 않는다.**
따라서 측정하려는 신호의 특성을 잘 파악해 이동평균의 데이터 개수를 선정해야 한다.
