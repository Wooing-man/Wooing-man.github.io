---
layout: single
title: "[차원 축소] 차원의 저주"
categories:	
    - dimensionality reduction
tags:
    - ['차원축소', '데이터 전처리', '데이터 엔지니어링']

toc: true
toc_sticky : true

author_profile: false
sidebar:
    nav: "docs"

last_modified_at:

---

# 차원의 저주(The curse of dimension)

---

## 차원의 저주란

- 데이터 학습을 위해 <span style='color: red'>차원이 증가</span>하면서 학습데이터 수가 차원의 수보다 적어져 <span style='color: red'>성능이 저하되는 현상</span>
- 차원이 증가할수록 변수가 증가하고, 개별 차원 내에서 학습할 데이터 수가 적어짐
- 일반적으로 **관측치**보다 변수 수가 많아지는 경우 발생
- 아래 그림과 같이 차원이 증가할수록 **빈 공간**이 많아짐(예를 들어, 관측치 개수는 200개, 변수는 700개)

![image](https://blog.kakaocdn.net/dn/6g8f5/btqv0yFthYE/NqRQzG0fTTuhYxeCJe865k/img.png)

<center>출처: 차원의 저주(The curse of dimension) 블로그</center>
<br/>

<br/>

## 왜 이런 현상이 발생하는가?

만약, 1차원 및 1변수라고 가정해본다. 

1차원은 '선'이며, 제일 왼쪽 그림처럼 선위에 관측치들이 표현될 것이다. 선 위에 데이터들은 나란히 있는 상황이다. 

여기서 차원만 1차원 -> 2차원 으로 늘려준다면 '평면'이 된다.(가운데 그림)

1차원일때보다 더 벌어져 있음을 알 수 있다

<br/>

여기서 차원만 2차원 -> 3차원 으로 더 늘려준다면 '입체'가 된다.(제일 오른쪽 그림)

점들 사이에 공간이 많이 비었음을 알 수 있다. 

이렇게 **차원이 증가함에 따라** 빈 공간이 생기는 것을 **차원의 저주**라고 한다.  

<br/>

##  빈 공간이 생기는 것이 왜 문제가 되는가?

빈 공간이 생겻다는 것은, 컴퓨터 상으로 0으로 채워졌다는 뜻이다. (정보가 없다는 것과 동일)

정보가 적으면 **모델의 성능이 저하될 수 밖에 없다.**  

<br/>

모델 중 KNN은 차원의 저주 문제에 가장 치명적인 알고리즘이다.

알고리즘 상으로 자신과 가장 가까운 이웃 K개를 보며 결과값을 정하게 되는데, 차원이 커질수록 주변의 이웃이 점점 멀어지게 되는 것이다.

이는 모델의 성능 뿐만 아니라 **계산비용 증가**에 대한 문제까지 발생시킨다.

---

# 참고 자료

- [차원의 저주 (The curse of dimensionality)](https://datapedia.tistory.com/15)