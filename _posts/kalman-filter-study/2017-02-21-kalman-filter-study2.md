---
layout: post
title: "Kalman Filter Study _ 1장. 평균 필터"
date: 2017-02-21
categories: [kalman_filter, matlab]
---

평균은 데이터의 총합을 데이터 개수로 나눈 값이다. 예를 들어 $k$개의 데이터가 있을때,
평균은 다음과 같이 계산한다.  

$$
\overline{x_k} = { x_1 + x_2 + \cdots + x_k \over k } \label{1.1}\tag{1.1}
$$

위 식은 데이터를 모두 모아서 한꺼번에 계산하는 식으로 배치식(`batch expression`)이다.
이 배치식에 데이터를 하나 추가하려면 모든 데이터를 다시 더해서 $k+1$로 나눠야 한다.
이 과정은 앞서 계산한 평균($\overline{x_k}$)을 전혀 써먹지 못한다. 그리하여 앞서 계산한
평균을 활용할 수 있는 재귀식이 필요하다.  

재귀식은 이전 평균값과 추가된 데이터 그리고 데이터 개수만 저장하면 된다. 데이터의 양이
늘어나면 배치식 보다 재귀식이 메모리 저장공간의 측면과 계산성능 측면에서 유리하다.
이제 식 \ref{1.1}을 재귀식으로 바꿔보자. 재귀식을 유도하는 과정에 필요한 $k-1$개의
평균 계산식을 사용한다.  

$$
\overline{x_{k-1}} = { x_1 + x_2 + \cdots + x_{k-1} \over k-1 } \label{1.2}\tag{1.2}
$$

식 \ref{1.1}의 양변에 $k$를 곱한다.  

$$
k\overline{x_k} = x_1 + x_2 + \cdots + x_k
$$

이 식의 양변을 다시 $k-1$로 나누면 다음과 같은 식이 된다.  

$$
{k \over k-1}\overline{x_k} = { x_1 + x_2 + \cdots + x_k \over k-1 }
$$

여기서 우변의 $x_k$를 따로 분리해서 두개의 항으로 만들면 다음의 식이 된다.  

$$
\begin{align}
{k \over k-1}\overline{x_k} &= { x_1 + x_2 + \cdots + x_k \over k-1 } \\\\
                            &= { x_1 + x_2 + \cdots + x_{k-1} \over k-1 } + { x_k \over k-1 }
\end{align}
$$

우변의 첫 번째 항은 식 $\ref{1.2}$의 $\overline{x_{k-1}}$의 정의와 같다. 따라서
위의 식은 다음과 같이 쓸 수 있다.  

$$
{k \over k-1}\overline{x_k} = \overline{x_{k-1}} + {x_k \over k-1}
$$

이 식의 양변을 ${k \over k-1}$로 나누면 다음과 같은 재귀식이 나온다.  

$$
\overline{x_k} = {k-1 \over k}\overline{x_{k-1}} + {1 \over k}x_k \label{1.3}\tag{1.3}
$$

식 $\ref{1.3}$을 이용해 평균을 계산하려면 직전 평균값 $\overline{x_{k-1}}$과 데이터
개수 $k$, 그리고 새로 추가된 데이터 $x_k$만 있어도 된다. 그러므로 평균 필터는 하나씩
차례로 입력되는 데이터의 평균을 계산할 때 유용하다.  

식 $\ref{1.3}$을 더 간결하게 표현해보자. $\alpha = {k-1 \over k}$라고 정의하면
$\alpha$와 $k$ 사이에는 다음과 같은 관계식이 성립한다.  

$$
\alpha = {k-1 \over k} = 1 - {1 \over k} \\\\
\therefore {1 \over k} = 1 - \alpha
$$

이제 $\alpha$의 정의와 위 식을 식 $\ref{1.3}$에 대입하여 정리하면 다음과 같은 재귀식을
얻을 수 있다.  

$$
\begin{align}
\overline{x_k} &= {k-1 \over k}\overline{x_{k-1}} + {1 \over k}x_k \\\\
               &= \alpha\overline{x_{k-1}} + {(1-\alpha)x_k} \label{1.4}\tag{1.4}
\end{align}
$$

식 $\ref{1.4}$의 이름은 **평균 필터**(`Averaging filter`)이다. 이제 평균 필터를
MATLAB의 함수로 구현한다. 이 함수는 측정값을 인자로 받아 평균을 반환한다.  

**AvgFilter.m**

```matlab
function [ avg ] = AvgFilter(x)
% 평균 필터 함수
%   재귀적 구현

persistent prevAvg k
persistent firstRun

if isempty(firstRun)  %
    k = 1;            %
    prevAvg = 0;      %  
                      % part A
    firstRun = 1;     %
end                   %

alpha = (k - 1)/k;
avg = alpha*prevAvg + (1-alpha)*x;

prevAvg = avg;
k = k+1;

end
```


MATLAB의 `persistent variable`은 함수의 호출이 끝난 뒤에도 값이 그대로 유지된다.
`C/C++`의 `static variable`처럼 생각하면 된다. 위 코드에서 `part A`는 초기화 부분으로
함수가 처음 호출될 때 한 번만 실행된다.  

다음은 위 평균 필터 함수를 테스트하는 예제이다.  

**TestAvgFilter.m**

```matlab
clear all

% 평균 필터 테스트
% 0.2초 간격으로 전압측정
dt = 0.2;
t = 0:dt:10;

Nsamples = length(t);

Avgsaved = zeros(Nsamples, 1);
Xmsaved = zeros(Nsamples, 1);

for k=1:Nsamples
    xm = GetVolt();
    avg = AvgFilter(xm);

    Avgsaved(k) = avg;
    Xmsaved(k) = xm;
end

figure
plot(t, Xmsaved, 'r:*')
hold on
plot(t, Avgsaved, 'o-')
```

**GetVolt.m**

```matlab
function [ z ] = GetVolt()
% 전압을 읽어오는 함수
%   평균 14.4 볼트, 잡음이 섞여있음 잡음의 평균은 0 이고 표준편차가 4

w = 0 + 4*randn(1,1);
z = 14.4 + w;

end
```


위 `GetVolt.m`은 전압값을 읽어오는 함수로 측정하는 전압은 평균이 $14.4$ 볼트이고 잡음이
섞여있다. 잡음의 평균은 $0$ 이고 표준편차가 $4$라고 가정한다. 잡음은 MATLAB의 내장함수
`randn`로 만들었다. 아래는 실행 결과이다.  

![AvgFilter_1](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/AvgFilter_1.png)

![gd]({{site.url}}/master/static/img/_posts/kalman-filter-study/AvgFilter_1.png)

정리하면 재귀식인 평균 필터는 직전 평균값과 데이터 개수만 알면 쉽게 평균을 구할 수 있다.
특히 데이터가 순차적으로 입력되는 경우에는 데이터를 저장할 필요가 없고 계산 효율도 높다.
만약 데이터를 실시간으로 처리해야 한다면 재귀식 형태의 필터가 필수다. 참고로 평균을 내면
잡음이 제거되는 효과가 있음!
