---
layout: single
title: "[밑시딥1_1] 퍼셉트론 AND, NANA, OR, XOR 논리회로 표현하기"
categories:	
    - deep learning
tags:
    - ['딥러닝', '알고리즘', '기초']

toc: true
toc_sticky : true

author_profile: false
sidebar:
    nav: "docs"

last_modified_at:
use_math: true
---

**본 포스팅은 수원대학교 한경훈 교수님의 강의 [딥러닝1](https://www.youtube.com/@SlowAI)을 바탕으로 학습한 내용입니다.**

----

# 퍼셉트론

- 프랑크 로젠플라트(Frank Rosenblatt)가 1957년에 고안안 알고리즘으로 신경망(딥러닝)의 기원이 되는 알고리즘임
- 다수의 신호(흐름이 있는)를 입력으로 받아 하나의 신호를 출력. 퍼셉트론은 이 신호를 입력으로 받아 '흐른다(0)/안 흐른다(1)'이라는 정보를 앞으로 전달

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F99BDCE4D5B98A1022C){: .align-center}

- 위의 그림에서
  - $x_1$과 $x_2$는 입력 신호, $y$는 출력 신호, $w_1$과 $w_2$는 가중치(weight)를 의미
  
  - 원을 뉴런(혹은 노드)라고 부름
  
  - 입력 신호가 뉴런에 보내질 때, 각각 고유한 <u>가중치<u>가 곱해진다.($w_1$$x_1$, $w_2$$w_2$)
  
  - 뉴런에서 전달 받은 신호의 총합이 임계값 $\theta$를 넘을 때만 1을 출력
  
  - 수식으로 표현하면
  
    $$y=\begin{cases}0&(w_1x_1+w_2x_2 \leq \theta) \\\ 1&(w_1x_1+w_2x_2 > \theta) \end{cases}$$

- 퍼셉트론은 복수의 입력 신호 가각에 고유한 가중치를 부여. 가중치는 각 신호가 결과에 주는 영향력을 조절하는 요소로 작용하며, 가중치가 클수록 해당 신호가 그만큼 더 중요함을 뜻함

<br/>

# AND 게이트

- AND 게이트는 입력이 모두 1일 경우에만 출력이 1이 됨

![and](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F99F746505B98A11837){: .align-center}

- 위의 표는 AND 게이트의 진리표이며, 퍼셉트론으로 표현으로 $w_1, w_2, \theta$는 아래와 같을 때 만족

 $$(w_1, w_2, \theta) = (0.5, 0.5, 0.7)$$

## 코드

~~~python
"file name : and_gate.py"

import numpy as np

def AND(x1, x2):
  x = np.array([x1, x2])
  w = np.array([0.5, 0.5]) # (가중치) w1: 0.5, w2: 0.5
  b = -0.7
  tmp = np.sum(x*w)+b
  if tmp <= 0:
    return 0
  else:
    return 1
  
 
if __name__ == "__main__":
  for xs in [(0, 0), (1, 0), (0, 1), (1, 1)]:
    y = AND(xs[0], xs[1])
    print(str(xs) + '-->' + str(y))
~~~

<br/>

# NAND 게이트

- NAND 게이트는 Not AND를 의미하며, AND 게이트의 반대를 출력

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F99B11F495B98A12C01){: .align-center}

- 퍼셉트론으로 표현은 아래와 같음

 $$(w_1, w_2, \theta) = (-0.5, -0.5, -0.7)$$

## 코드

~~~ python
"file name : nand_gate.py"

import numpy as np

def NAND(x1, x2):
  x = np.array([x1, x2])
  w = np.array([-0.5, -0.5])
  b = 0.7
  tmp = np.sum(x*w)+b
  if tmp <= 0:
    return 0
  else:
    return 1
  
 
if __name__ == "__main__":
  for xs in [(0, 0), (1, 0), (0, 1), (1, 1)]
  y = NAND(xs[0], xs[1])
  print(str(xs) + '-->' + str(y))
~~~

<br/>

# OR 게이트

- 입력 신호 중 하나 이상이 1이면 1을 출력

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F998CFD4F5B98A13A32){: .align-center}

- 퍼셉트론으로 표현은 아래와 같음

$$(w_1, w_2, \theta) = (0.5, 0.5, 0.2)$$

## 코드

~~~python
"file name : or_gate.py"

import numpy as np

def OR(x1, x2):
  x = np.array([x1, x2])
  w = np.array([0.5, 0.5])
  b = -0.2
  tmp = np.sum(w*x) + b
  if tmp <= 0:
    return 0
  else:
    return 1
  
 
if __name__ == "__main__":
  for xs in [(0, 0), (1, 0), (0, 1), (1, 1)]:
        y = OR(xs[0], xs[1])
        print(str(xs) + " -> " + str(y))
~~~

<br/>

# XOR 게이트

- 위의 게이트들은 선형으로만 표현이 가능할 뿐 비선형에 대한 문제는 풀 수 없음
- XOR 게이트는 비선형 문제에서 사용
- 입력 신호 중 하나라도 다르면 1, 둘다 같으면 0

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F992152485B98A1C705){: .align-center}

![](https://mblogthumb-phinf.pstatic.net/MjAxOTA4MTdfMTk5/MDAxNTY2MDAzNjg4MTE3.9y3O_1k4p8-zUeEP7sN_WYWkIB4rElVMl8isWkFZAN8g.8tblY85F4FGg3YjYZrSvKendg4882tkB4Z3vg4sPmRwg.PNG.cni1577/%EC%BA%A1%EC%B2%98.PNG?type=w800){: .align-center}

- AND(NAND게이트의 결과값,  OR게이트의 결과값)로 표현할 수 있음

## 코드

~~~python
"file name : xor_gate.py"

from and_gate import AND
from nand_gate import NAND
from or_gate import OR

def XOR(x1, x2):
    s1 = NAND(x1, x2)
    s2 = OR(x1, x2)
    y = AND(s1, s2)
    return y


if __name__ == '__main__':
    for xs in [(0, 0), (1, 0), (0, 1), (1, 1)]:
        y = XOR(xs[0], xs[1])
        print(str(xs) + " -> " + str(y))
~~~









<br/>

<br/>

# 참고자료

---

- [TeX_및_LaTeX_수식_문법](http://tomoyo.ivyro.net/123/wiki.php/TeX_%EB%B0%8F_LaTeX_%EC%88%98%EC%8B%9D_%EB%AC%B8%EB%B2%95)

- [퍼셉트론 - Perceptron](https://excelsior-cjh.tistory.com/169)
