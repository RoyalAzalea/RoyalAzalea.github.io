---
layout: post
title: "Kalman Filter Study _ 12장. "
date: 2017-03-24
categories: [kalman_filter, matlab]
---

이 장에서는 칼만 필터로 2차원 평면 위의 물체를 추적하는 방법에 대해 알아본다. 이 문제는
2차원 영상에서 특정 표적을 감시하고자 할 때 자주 등장한다. 예를 들어 레이더 영상에 나타난
표적을 추적한다거나, 카메라로 특정 대상을 실시간으로 감시하는 시스템 등을 개발할 때
필요한 기술이다.  

먼저 분명히 해두어야 할 사항이 있다. 표적의 위치는 영상처리 알고리즘으로 알아내야 한다.
칼만 필터는영상에서 물체의 위치를 찾아내는 능력이 없다. 칼만 필터는 영상처리 기법으로
찾아낸 물체의 위치를 입력 받아 정확한 위치를 추정하는 역할을 한다. 앞에서 살펴 본 칼만
필터 예제를 상기해 보자. 전압이나 거리의 측정은 칼만 필터의 역할이 아니고 센서가 하는
일이였다. 영상 속의 표적을 추적하는 문제도 마찬가지다. 표적의 위치는 적절한 영상처리
기법을 동원해서 찾아내야 한다. **다시 한번 강조하지만 영상에서 표적의 위치를 찾아내는
일은 칼만 필터의 담당이 아니다.**  

그렇다면 칼만 필터의 역할은 무엇일까? 이 질문에 대한 답은 앞 장의 예제에 다 나와 있다.
그동안 칼만 필터가 어떤 용도로 쓰였는지를 살펴 보면 된다는 뜻이다. **지금까지 칼만 필터는
잡음을 제거하거나 측정하지 않은 값을 추정해내는 등의 역할을 했다.** 표적 추적 문제에서도
칼만 필터의 역할은 동일하다. **영상처리 알고리즘으로 얻은 위치의 오차를 제거하고 이동 속도
등을 추정하는데 활용된다.**  

## 12.1 시스템 모델  

평면 상의 표적 추적 문제에 칼만 필터를 적용하기 위해서는 앞 장에서 소개한 위치-속도 모델을
2차원으로 확장해야 한다. 즉 상태 변수를 가로 방향($x축$) 위치-속도와 세로 방향($y축$)
위치-속도로 잡고 시스템 모델을 유도한다.  

$$
x = \begin{Bmatrix} 위치_x \\\\ 속도_x \\\\ 위치_y \\\\ 속도_y \end{Bmatrix}
$$  

여기서 상태 변수의 순서는 중요하지 않다. 예를 들어 위치 변수 두 개를 먼저 쓰고, 속도는
속도끼리 모아도 된다. 어쨌든 상태 변수를 위와 같이 정의하면 시스템 모델은 다음과 같이
주어진다.  

$$
x_{k+1} = Ax_k + W_k \\\\
z_k = Hx_k + v_k \label{12.1}\tag{12.1}
$$  

$$
A = \begin{bmatrix} 1 & \triangle t & 0 & 0 \\\\ 0 & 1 & 0 & 0 \\\\ 0 & 0 & 1 & \triangle t \\\\ 0 & 0 & 0 & 1 \end{bmatrix} , \;
H = \begin{bmatrix} 1 & 0 & 0 & 0 \\\\ 0 & 0 & 1 & 0 \end{bmatrix} \label{12.2}\tag{12.2}
$$  

행렬 $A$를 식 $\ref{12.1}$에 대입해서 정리해보면, $x$ 축과 $y$ 축 방향으로 각각 다음과
같은 관계를 행렬식으로 표현하고 있음을 알 수 있다.  

$$
\begin{Bmatrix} 위치_{k+1} \\\\ 속도_{k+1}\end{Bmatrix} = \begin{Bmatrix}  위치_{k} + 속도_{k} \cdot \triangle t \\\\ 속도_{k} \end{Bmatrix} + w_k
$$  

이 두 식이 갖는 의미는 11장에서 이미 설명했다. 그리고 행렬 $H$를 식 $\ref{12.1}$의
측정값 관계식에 대입해서 정리해보면, **$x$ 축과 $y$ 축 방향의 위치만 측정하고 속도는
측정하지 않는다는 의미가 담겨 있다.**  

지금까지의 내용을 정리해 보겠다. 평면 상의 물체를 추적하는 칼만 필터의 시스템 모델은 식
$\ref{12.1} ~ \ref{12.2}$와 같이 주어진다. 이 모델은 위치-속도 사이의 관계를 2차원
평면으로 확장한 것이다. 그리고 대상 표적의 위치만 영상처리 알고리즘으로 찾아서 칼만 필터에
입력한다.  

시스템 모델을 보고 이미 짐작하셨겠지만, 이 장의 내용은 2차원으로 확장한 것 말고는 11장과
별다른 차이가 없다. 즉 11장에서 소개한 칼만 필터를 2차원 문제에 확장해서 적용해 보는
정도에 불과하다. 그렇지만 실용적인 응용 사례를 구체적으로 다뤄본다는 측면에서는 나름
의미가 있다. 더구나 영상처리 기술이 다양한 분야에서 점점 더 많이 활용되는 추세이니 칼만
필터와 영상처리 알고리즘을 함께 활용하는 방법을 알아두면 도움이 될 것이다.  

## 12.2 칼만 필터 함수  

아래 `TrackKalman` 함수는 영상처리 알고리즘이 찾아낸 위치($xm, ym$)를 인자로 받아,
추정 위치($xh, yh$)를 반환한다. 코드의 마지막 줄에 상태변수 추정값에서 위치만 따로
뽑아서 반환하는 코드가 있다. 만약 추정 속도도 필요하다면 변수 $x$의 두 번째와 네 번째
원소를 함께 반환하면 된다.  

**TrackKalman.m**

```matlab
function [xh yh] = TrackKalman(xm, ym)
% 위치를 인자로 받아 추정 위치를 반환하는 함수
% 영상처리 알고리즘이 찾아낸 위치(xm,ym)를 인자로 받아, 추정 위치(xh, yh)를 반환함
persistent A H Q R
persistent x P
persistent firstRun


if isempty(firstRun)
  dt = 1;

  A = [ 1  dt  0   0
        0  1   0   0
        0  0   1  dt
        0  0   0   1 ];

  H = [ 1  0  0  0
        0  0  1  0 ];

  Q = 1.0*eye(4);
  R = [ 50  0
         0 50 ];

  x = [0, 0, 0, 0]';
  P = 100*eye(4);

  firstRun = 1;
end


xp = A*x;
Pp = A*P*A' + Q;

K = Pp*H'*inv(H*Pp*H' + R);

z = [xm ym]';
x = xp + K*(z - H*xp);
P = Pp - K*H*Pp;

% 추정 위치 가져옴
xh = x(1);
yh = x(3);
```


## 12.3 테스트 프로그램  

이제 `TrackKalman` 함수가 제대로 작동하는지 확인해 보자. 아래 그림과 같이 공이 오른쪽
상단에서 왼쪽 하단까지 대각선 방향으로 이동하는 상황을 고려한다. 공의 이동 속도는
일정하고, 공의 이동 영상은 $1$초에 한 번씩 촬영된다고 가정한다.  

![Track_1_1](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/Track_1_1.PNG)  

아래 `TestTrackKalman.m`은 테스트 프로그램이다. 촬영 영상에서 공의 위치를 찾아내는
알고리즘은 `GetBallPos` 함수에 구현되어 있다. 테스트 프로그램은 `GetBallPos` 함수로
공의 위치를 측정해서 칼만 필터에 입력으로 주고, 칼만 필터에서 추정 위치를 전달 받는다.  

**TestTrackKalman.m**

```matlab
clear all

NoOfImg = 24;

Xmsaved = zeros(2, NoOfImg);
Xhsaved = zeros(2, NoOfImg);

% 이미지 보여주고 측정 위치, 예측 위치 띄워 줌
for k = 1:NoOfImg
  [xm, ym] = GetBallPos(k);
  [xh, yh] = TrackKalman(xm, ym);

  hold on
  plot(xm, ym, 'r*')
  plot(xh, yh, 'bs')
  xlabel('Horizonal[pixel]')
  ylabel('Vertical[pixel]')

  pause(1)

  Xmsaved(:, k) = [xm ym]';
  Xhsaved(:, k) = [xh yh]';

end


figure
hold on
plot(Xmsaved(1,:), Xmsaved(2,:), '*')
plot(Xhsaved(1,:), Xhsaved(2,:), 's')
xlabel('Horizonal[pixel]')
ylabel('Vertical[pixel]')
legend('Measured','Kalman Filter','location','NorthEast')
```  


위 프로그램에서 `for` 문 내부에 있는 `plot` 함수는 촬영 이미지 위에 측정 위치와 추정
위치를 표시한다. `GetBallPos` 함수로 측정한 위치는 `*` 모양으로, 칼만 필터가 추정한
위치는 사각형(`□`)으로 표시한다. 테스트 프로그램을 실행하면 측정 위치와 추정 위치가
표시된 촬영 이미지가 $1$초마다 새로 그려진다.  

아래 `GetBallPos` 함수는 영상에서 공의 위치를 찾아낸다. 모든 영상 이미지는 `img` 라는
서브 폴더에 있다고 가정했다. 영상에서 물체를 찾아내는 알고리즘은 매우 다양한데,
여기서는 배경이미지(`imgBg`)와 촬영 이미지(`imgWork`)의 차이를 이용하는 가장 간단한
알고리즘을 사용했다. 영상처리 알고리즘에 대한 자세한 설명은 `MATLAB`의
`Image Processing Toolbox` 문서를 참고하면 된다.  

그리고 함수의 마지막 두 줄은 공의 중심 좌표를 반환하는 코드인데, 임의로 평균 $15$ 픽셀
정도의 잡음을 섞었다.  

**GetBallPos.m**

```matlab
function [xc, yc] = GetBallPos(index)
% 영상에서 공의 위치를 찾아내는 함수
% 배경 이미지와 촬영 이미지의 차이를 이용하여 구함
persistent imgBg
persistent firstRun

if isempty(firstRun)
  imgBg = imread('Img/bg.jpg');

  firstRun = 1;
end

xc = 0;
yc = 0;

imgWork = imread(['Img/', int2str(index), '.jpg']);
imshow(imgWork)

fore = imabsdiff(imgWork, imgBg);
fore = (fore(:,:,1) > 10) | (fore(:,:,2) > 10) | (fore(:,:,3) > 10);

L     = logical(fore);
stats = regionprops(L, 'area', 'centroid');
area_vector = [stats.Area];
[tmp, idx] = max(area_vector);

centroid = stats(idx(1)).Centroid;

% 임의로 평균 15 픽셀 정도의 잡음을 섞음
xc = centroid(1) + 15*randn;
yc = centroid(2) + 15*randn;
```  


아래는 수행 결과 그림이다. 그림에서 `*` 모양으로 표시된 지점은 `GetBallPos` 함수의
측정값이고, 네모 모양은 칼만 필터의 추정 위치를 의미한다. 그리고 수행과정은 오른쪽
하단에서 시작되서 왼쪽 상단으로 진행된다.  

![Track_1](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/Track_1.PNG)  

이제 테스트 프로그램의 수행 결과를 검토해보자. 칼만 필터의 추정 위치가 대각선에 더
가깝게 배치되어 있다. 즉, 칼만 필터가 측정 잡음을 효과적으로 제거해서 더 참 값에
가까운 위치를 출력한다. 그런데 그림에서 공이 오른쪽 하단에 있을 때 한 점이 대각선에서
많이 벗어나 있는데, 그 이유는 초기 위치 오차 때문이다. `TrackKalman` 함수를 보면
초기 위치가 $(0,0)$으로 지정되어 있다. 즉 초기에 공이 왼쪽 상단에 있다고 가정하고 있다.
테스트 프로그램이 막 시작할 때는 칼만 필터가 충분히 수렴하기 전이라 오차가 크게
나타난 것이다.  

## 12.4 테스트 프로그램 2  

이 절에서는 행렬 $Q$와 $R$을 조율해서 칼만 필터의 성능을 개선하는 방법을 소개한다.
앞 절의 예제에서 $Q$와 $R$을 바쒀가면서 추정 성능이 어떻게 달라지는지를 살펴보겠다.  

먼저 8.3 절의 내용을 회고해 보겠다. 행렬 $Q$와 $R$은 칼만 필터의 성능에 정반대의 영향을
준다. 행렬 $Q$의 크기를 키우면 칼만 이득이 커지게 되고 , 결과적으로 측정값의 반영 비율이
더 높아진다. **즉 추정 결과가 측정값에 더 가까워진다.** 반대로 행렬 $Q$의 크기를 줄이면
추정 결과는 측정값의 영향을 덜 받아 직전 추정값과 비슷해진다. **즉 측정값에 포함된 잡음의
영향이 줄어 추정값이 더 매끄러운 궤적을 보인다.**  

반면 행렬 $R$은 $Q$와 정반대이다. **추정 결과에 측정값을 많이 반영하려면 행렬 $R$의
크기를 줄여야한다. 반대로 측정값의 영향을 줄이고 더 부드러운 궤적의 추정 결과를 원하면
행렬 $R$의 크기를 키워야 한다.**  

행렬 $Q$와 $R$이 추정값에 미치는 영향을 알아보기 위해 `TrackKalmanQR` 이라는 함수를
추가했다. 테스트 프로그램 `TestTrackKalmanQR.m`에서는 이 함수와 `TrackKalman` 함수의
추정 결과를 비교한다.  

**TrackKalmanQR.m**

```matlab
function [xh yh] = TrackKalmanQR(xm, ym)
% 위치를 인자로 받아 추정 위치를 반환하는 함수
% TrackKalman과 동일하나 Q와 R을 바꿔가며 실험
persistent A H Q R
persistent x P
persistent firstRun


if isempty(firstRun)
  dt = 1;

  A = [ 1  dt  0   0
        0  1   0   0
        0  0   1  dt
        0  0   0   1 ];

  H = [ 1  0  0  0
        0  0  1  0 ];

  % Q와 R을 변경하면서 실험 진행
  Q = 0.1*1.0*eye(4);
  R = 0.1*[ 50  0
         0 50 ];

  x = [0, 0, 0, 0]';
  P = 100*eye(4);

  firstRun = 1;
end


xp = A*x;
Pp = A*P*A' + Q;

K = Pp*H'*inv(H*Pp*H' + R);

z = [xm ym]';
x = xp + K*(z - H*xp);
P = Pp - K*H*Pp;


xh = x(1);
yh = x(3);
```

**TestTrackKalmanQR.m**

```matlab
clear all

NoOfImg = 24;

Xmsaved  = zeros(2, NoOfImg);
Xhsaved  = zeros(2, NoOfImg);
Xqrsaved = zeros(2, NoOfImg);

for k = 1:NoOfImg
  [xm,   ym] = GetBallPos(k);
  [xh,   yh] = TrackKalman(xm, ym);
  [xqr, yqr] = TrackKalmanQR(xm, ym);

  hold on
  plot(xm,  ym,  'r*')
  plot(xh,  yh,  'bs')
  plot(xqr, yqr, 'bo')

  pause(1)

  Xmsaved(:, k)  = [xm   ym]';
  Xhsaved(:, k)  = [xh   yh]';
  Xqrsaved(:, k) = [xqr, yqr];
end

x = [0 350];
y = [250 0];

figure
hold on
%plot(Xmsaved(1,:),  Xmsaved(2,:),  '*')
plot(Xhsaved(1,:),  Xhsaved(2,:),  's')
plot(Xqrsaved(1,:), Xqrsaved(2,:), 'o')
plot(x,y) % 비교 선
xlabel('Horizonal[pixel]')
ylabel('Vertical[pixel]')
legend('Q and R','1/10 Q and 1/10 R','location','NorthEast')
```


아래 그래프에 행렬 $Q$의 크기를 줄였을 때의 추정 결과를 비교했다. 그림을 보면 **행렬
$Q$가 더 작은 칼만 필터의 추정값은 잘 움직이지 않는다는 사실을 알 수 있다. 앞에서
설명한 것처럼 측정값보다는 직전 추정값이 더 많이 반영되기 때문이다.**  그리고
행렬 $Q$의 크기를 $1/100$로 줄였을 때 보다 $1/1000$으로 줄였을 때 시간 지연이 커진것을
확인할 수 있다.  

![Track_2](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/Track_2.PNG)  

![Track_3](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/Track_3.PNG)  

아래 그래프에 행렬 $R$의 크기를 줄였을 때의 추정 결과를 비교했다. 그림을 보면 행렬 $R$의
크기를 줄였을 때 추정값들이 대각선에서 많이 벗어났다. **직전 추정값보다는 측정값이 더
많이 반영되어 시간 지연은 줄었으나 잡음이 잘 제거되지 않았다.**

![Track_4](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/Track_4.PNG)  

아래 그래프에 행렬 $Q$와 $R$의 크기를 동시에 줄였을 때의 추정 결과를 비교했다. 그림을
보면 크기를 $1/100$로 줄였을 때를 보자. 반 정도 진행된 이후, 두 함수가 같은 추정값을
내놓았다. 두 행렬의 크기를 $1/10$로 줄였을 때는 반 정도 진행된 이전부터 두 함수가 같은
추정값을 내놓았다.  

![Track_5](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/Track_5.PNG)  

![Track_6](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/kalman-filter-study/Track_6.PNG)  
