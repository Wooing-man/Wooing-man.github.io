---
layout: single
title: "[컴퓨터비전] 카메라 캘리브레이션 (영상의 기하학적 해석)"
categories:	
    - cv
tags:
    - ['딥러닝', '비전', '전처리']

toc: true
toc_sticky : true

author_profile: false
sidebar:
    nav: "docs"

last_modified_at:
use_math: true
---

# 영상의 기하학적 해석 - 영상의 지면 투영 (ground projection)

----

- 카메라 영상에서 어떤 물체를 발견했을 때, <u>해당 물체가 카메라로부터 실제로 얼마나 멀리 떨어져 있으며, 크기는 얼마인지 알아내는 것</u>은 영상기하학의 응용 중 하나임.
  - 예를 들면, CCTV 카메라에 잡힌 물체의 실제 거리와 크기를 알아내거나 자동차에 달린 블랙박스 카메라에서 전방의 물체를 자동으로 인식하여 물체의 크기 및 남은 거리, 이동속도를 추정하는 등이 있음.
  
- __<font color='red'>문제 정의</font>__
  
  - 아래 그림에서 자동차와 보행차를 찾았다고 하자.
  
  ![img1](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F2178FB4757884AC80E){: width="70%" height="70%"}{: .align-center}
  
  - 알아내고 싶은 것 : 이들의 물체가 카메라로부터 실제 얼마나 멀리 떨어져 있고 크기가 얼마인지.
  - 주의! 여기서 물체의 '위치'는 3D 좌표가 아니라 2D 좌표임.
  - 이 문제에서 <u>불변의 가정은 물체는 땅(지면)에 붙어있다임.</u>
  - 우리가 구하고자 하는 물체의 위치란 <u>물체가 지면과 맞닿은 지점(중점)의 2D 좌표임 (그림에서 A 지점)</u>
    - <u>즉, 영상 내 임의의 영상좌표(픽셀좌표)에 대해 대응되는 2D 지면좌표(ground plane coordinate)를 구하는 문제로 귀결됨.</u>
  
  - <font color=blue>정리</font>
    1. 카메라의 높이와 방향은 고정 (카메라가 벽 혹은 자동차에 부착된 경우 등)
    2. 물체는 항상 지면에 붙어있음
    3. 카메라 파라미터(렌즈 왜곡계수, 높이, 각도 등)를 안다고 가정
    4. 풀고자 하는 문제는 임의의 영상좌표에 대응되는 2D 지면좌표

<br>

# 접근 방법

----

- 손과 연필을 이용해 기하학적으로 직접 푸는 방법
- 호모그래피(homography)를 이용한 방법
- 3D 변환을 이용한 방법

## 1. 직접적인 기하학적 계산 방법

- 손으로 계산하는 방법이 가장 어렵고 복잡하지만 이해하기엔 가장 도움이 될 것.
- 아래 그림을 살펴보면, 카메라 원점(C)와 영상좌표 (p), p에 대응되는 지면좌표(P)는 일직선상에 존재함.

​	![img2](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F2527E84A578880DE12){: width="70%" height="70%"}{: .align-center}

- 카메라 내부파라미터(초점거리(f_x, f_y), 주점(c_x, c_y), 지면에서의 높이(h), 카메라의 틸트($\theta_{tilt}$)는 알고 있다고 가정. ( $\theta_{tilt}$는 카메라 방향이 지면과 수평일 때 0, 위쪽을 바라볼 때 +, 아래쪽을 바라볼 때 - )

- 입력 영상 픽셀좌표: p(x, y), 대응되는 지면좌표: P(X, Y) 가정.

- 우선, 카메라의 내부파라미터에 대한 영향을 없애기 위해 픽셀좌표를 <u>정규좌표로 변환.</u>

  ![img3](https://t1.daumcdn.net/cfile/tistory/2618F0425789DF5F0C){: .align-center}

- 정규 이미지 평면에서 주점의 좌표 c(0, 0)는 0이 되고, 이미지 평면과 카메라 원점과의 초점거리가 1이 됨.

- 구해진 정규좌표 (u, v)와 카메라 높이 h, 틸트 $\theta_{tilt}$를 이용한 이후의 계산과정은 아래와 같음.

  ![img4](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F2125DD3E5788A44731){: width="70%" height="70%"}{: .align-center}

- 위 계산 결과는 $d$, $\theta$는 다음과 같음

  -  $d$: 카메라 원점과 물체와의 <u>지면에서의 직선거리.</u>

  - $\theta$: 카메라 광학축을 기준으로 한 물체의 방향각 (광학축 기준 왼쪽은 +, 오른쪽은 -)

  - top-view로 보면 아래와 같음.

    ![img4](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F2415DE3D5788A53E08){: width="70%" height="70%"}{: .align-center}

여기까지 해서 물체가 카메라로부터 어떤 방향($\theta$)에 있고 얼마나 떨어져($d$) 있는지 계산할 수 있게 되었고, 마지막으로 물체의 위치를 지면좌표로 표현하면 다음과 같다. (단, 카메라 위치를 지면좌표계의 원점, 광학축 방향을 X축으로 가정했을 때의 좌표임.)

![img5](https://t1.daumcdn.net/cfile/tistory/256116435789DFDB36){: .align-center}

- 단, 카메라의 roll이 0인 경우에만 성립함. 0이 아닌 경우 방법2 혹은 방법3을 이용해야 함.
- P(X, Y)는 지면좌표계에 대한 정의에 따라 달라질 수 있으며 카메라 광학축 방향을 X축을 잡았을 경우의 좌표임.

<br>

## 2. 호모그래피(Homography)를 이용한 방법

- 호모그래피를 이용한 방법은 실용적으로 가장 간편한 방법이며 특별한 이론적 지식 없이 활용 가능.

- 호모그래피란 <u>한 평면을 다른 평면에 투영(projection) 시켰을 때, 투영된 대응점들 사이에 일정한 변환관계가 성립</u>하는데 이때의 변환관계를 말함.

  ![img6](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F2747EE3D5789D02518){: width="70%" height="70%"}{: .align-center}

- 호모그래피는 3x3 행렬로 표현되며 대응점들의 동치좌표(homogeneous coordinate) 표현에 대해 성립하는 변환관계임.

- 즉, 한 평면 위의 점들 (x1, y1), (x2, y2), ... 이 다른 평면 위의 점들 (x1', y1'), (x2', y2'), ... 로 각각 투영되었다면, 이들 대응점들 사이에 다음 관계식을 만족하는 3x3 호모그래피 행렬 H가 항상 존재하며 또 유일하게 존재함. (scale factor는 무시할 경우)

  ![img7](https://t1.daumcdn.net/cfile/tistory/27725F445789E00309){: .align-center}

- $s$는 동치좌표 표현에 있어서 scale factor임. 동치좌표 표현에 대해선 [다크프로그래머님의 글](https://darkpgmr.tistory.com/78)을 참조.

- 여기서, <u>H가 유일하게 존재한다는 점에 주목!</u>

- 이 말은 p1, p2, p3, p4를 이용해서 H를 구하나 p2, p3, p4, p5를 이용해서 H를 구하나 그 결과가 동일하다는 뜻임. (일반적으로 호모그래피를 결정하기 위해선 4개의 대응쌍이 필요함.)

- 결과적으로 함축적인 의미는 <u>임의의 네 대응쌍을 이용해서 구한 H를 다른 모든 점들에 대해서도 동일하게 적용할 수 있다</u>는 것임.

  - 예시를 살펴보면, 서로 다른 시점의 두 이미지 A, B가 있음.
  - 두 이미지에서 4개의 대응쌍 p1-p1', p2-p2', p3-p3', p4-p4'를 찾아서 이들 대응 좌표들로부터 호모그래피 H를 구했다고 하면, <u>구한 H는 다른 점들에 대해서도 동일하게 적용된다는 뜻임.</u> 즉, p5에 대응되는 이미지 B에서의 좌표는 H*p5로 구할 수 있으며, 다른 모든 대응점들에 대해서도 p' = Hp가 성립함.

  ![img8](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F24694E425789EEDE10){: width="70%" height="70%"}{: .align-center}

<br>

엇 근데 뭔가 이상해. 이상함을 느껴야함. 왜 why?

- 호모그래피는 투영관계에 있는 두 평면 사이의 변환관계라 했음. But, 위의 그림에선 서로 다른 두 이미지에 투영된 마우스패드 좌표들 사이의 대응관계에 대한 호모그래피를 사용했음. what!?

- 호모그래피는 직접적인 투영관계에 있는 두 평면 사이에서 뿐만 아니라 <u>투영관계에 의해 직·간접적으로 연결되는 모든 평면들 사이에서도 일반적으로 성립함.</u>

  - 즉, 한 평면을 평면 A와 평면 B에 각각 투영했을 때, 평면 A에 투영된 좌표와 평면 B에 투영된 좌표 사이에서도 호모그래피가 성립한다는 것임.
  -  그 이유는 원래 평면에서의 좌표를 p, 평면 A에 투영된 좌표를 p', 평면 B에 투영된 좌표를 p'', 원래 평면과 평면 A와의 호모그래피를 H1, 평면 B와의 호모그래피를 H2라 하면 p' = $H1$p, p'' = $H2$p = $H2$($H1^{-1}$p') = $H2H1^{-1}$p'가 성립하는 것임.
  - 즉, 평면 A와 평면 B 사이에는 호모그래피 $H = H2H1^{-1}$가 성립함. 아래 그림의 경우 평면 위의 점들이 두 이미지 평면 A, B에 투영된 경우로서 역시 호모그래피가 성립함.

  ![img9](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F22638F4E578A0BB114){: width="70%" height="70%"}{: .align-center}

- 단, 주의할 점은 <u>호모그래피는 평면형 물체에 대해서만 성립</u>한다는 점임.
- 입체를 가진 선풍기, 자동차, 아파트(한쪽 벽만 고려할 경우는 가능) 등은 성립하지 않음.
- 위의 문제 정의의 경우 지면과 이미지 평면과의 관계이므로 호모그래피가 성립함.
- 방법
  - 먼저, 카메라 시야 내 바닥에 임의의 네 점을 잡고 이들의 지면좌표를 실측을 통해 측정. 지면좌표계의 기준(원점 등) 자신이 원하는 데로 잡음.
    - CCTV 등과 같이 건물 내에 고정된 카메라라면 벽의 한쪽 모서리 등을 원점으로 잡는게 실측에 편리할 것이고, 자동차에 부착된 카메라라면 자동차의 앞바퀴 중심이나 카메라를 원점으로 잡는게 편할 것.
    - 어쨌든 자신이 잡은 기준(지면좌표계)에 맞춰 네 점의 지면좌표를 측정한 후 해당 점들의 영상좌표(카메라 이미지에서의 픽셀좌표)를 구하기.
  - 픽셀좌표로부터 지면좌표로의 호모그래피 변환행렬 H의 계산은 opencv의 findHomogray함수를 이용하거나 다른 코드 참조.
  - 행렬 H를 얻으면,  임의의 영상 픽셀좌표 p(x, y)에 대응되는 지면좌표는 p를 동차좌표로 확장한 (x, y, 1)에 H를 곱한 결과를 다시 2d 좌표로 변환하면 얻어짐.
  - 즉, H*(x, y, 1)T = (a, b, c)T라면 구하고자 하는 지면좌표는 (a/c, b/c)



<br>

## 3. 3D 변환을 이용한 방법

- 가장 일반적인 방법

- 카메라의 내부 파라미터(fx, fy, cx, cy) 및 3D 자세정보(R, t)는 미리 주어져 있다고 가정.

- 어떤 3차원 공간상의 한 점 P에 대한 카메라 좌표(camera coordinate)를 Pc, 월드좌표(world coordinate)를 Pw라 하면, Pc와 Pw 사이의 변환은 다음 수식에 의해 주어짐.

  ![img10](https://t1.daumcdn.net/cfile/tistory/24580535578AC57C31){: .align-center}

- 입력 영상 픽셀좌표를 p(x,y), 대응되는 지면좌표를 P(X, Y)라 하자.

  - 이 때, 구한 정규좌표를 3차원 카메라좌표로 해석하면 (u, v, 1)이 됨.
  - 정규이미지평면은 카메라 원점에서 초점거리가 1인 평면이므로 정규이미지 평면 상의 점 (u, v)의 카메라좌표계 좌표는 (u, v, 1)이 됨. 이 점을 Pc = (u, v, 1)이라 하자.

  ![img11](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F261A4E4C578AE16A33){: width="70%" height="70%"}{: .align-center}

  - 이제 카메라 원점과 Pc를 연결한 직선이 지면과 만나는 점을 구하면 원하는 답을 구할 수 있음!

  - 지면과의 교점을 구하기 위해서는 카메라좌표계가 아닌 월드좌표계에서 계산을 수행해야 함!

  - 카메라 원점의 카메라좌표를 $C_{c}$, 월드좌표를 $C_{w}$, 점 Pc의 월드좌표를 Pw라 하자.

  - 카메라 원점의 카메라좌표는 항상 (0, 0, 0)임을 주의하고(즉, Cc = (0, 0, 0)),대응되는 월드좌표는 위의 계산식에서 구할 수 있음. 

    - 이제 월드좌표계 상에서 Cw와 Pw를 잇는 직선이 지면과 만나는 점을 구하면 됨. [다크프로그래님의 좌표계 참조](https://darkpgmr.tistory.com/32)

    - 벡터(vector)의 개념을 이용하면 Cw와 Pw를 잇는 직선상의 임의의 점은 P = Cw + k(Pw - Cw)로 표현할 수 있음. (k는 임의의 상수)

      ![img12](https://t1.daumcdn.net/cfile/tistory/2333F84B578AE3BC09){: .align-center}

    - 그런데, 여기서 월드좌표계 상에서의 지면은 Z = 0 인 경우이므로 Cw + k(Pw - Cw)의 Z좌표가 0이 되도록 k의 값을 구한 후 위의 식에 대입하면 원하는 지면좌표 P가 구해짐.

    - 주의! 여기서 구한 지면좌표는 월드좌표계를 어떻게 설정하느냐에 따라 달라지는 값임.



<br>

## 물체의 크기 구하기

- 앞서 방법들을 이용하여 물체의 위치(지면좌표)가 구해지면 크기(폭, 높이)를 구하는 것은 비례식을 이용해 쉽게 계산 가능.

  ![img13](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F2274FD3C578AEEC71B){: width="70%" height="70%"}{: .align-center}

- 카메라의 지면에서의 높이를 hcam, 구하고자 하는 물체의 키를 hobj라 하자. (카메라의 높이는 미리 알고 있다고 가정한다.)
- <u>카메라와 물체와의 거리(d1)은 앞서 방법을 통해 이미 구해진 상태임.</u> 
- 따라서 이제 물체의 발끝이 아닌 <u>머리끝 지점에 대해 지면좌표를 앞서 방법으로 구하면 됨.</u>
- 그러면 이렇게 구한 지면좌표는 위의 그림에서와 같이 <u>카메라와 물체의 최상단 끝을 연결한 선이 지면과 만나는 지점임.</u> 이 거리를 d2라 하자.
- 마지막으로 삼각형의 비례관계에 의해 hcam : hobj = d2 : d2-d1이 성립되면서, hobj = hcam*(d2-d1)/d2로 계산하면 됨.







<br>

<br>

# 참고자료

---

- [https://darkpgmr.tistory.com/153](https://darkpgmr.tistory.com/153)
