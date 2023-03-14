---
layout: single
title: "[통계] Multivariable / Multivariate 차이"
categories:	
    - statistic
tags:
    - ['통계', '시계열', '기본지식']

toc: true
toc_sticky : true

author_profile: false
sidebar:
    nav: "docs"

last_modified_at:
---


# Multivariable / Multivariate 의 차이점 알아보기
----

- Multivariable과 Multivariate를 혼동하는 경우가 많은데, 엄연히 다른 의미를 가진 분석이다.
- 이를 설명하기 위해 우선 Univariate와 Univariable의 개념을 먼저 비교해본다.

<br/>

# Univariate vs Multivariate (단변량 vs 다변량)

- **종속변수 Y**의 개수에 따라 구분이 가능하다.
- Univariate : <u>종속변수가 하나</u>인 경우. 대부분의 분석이 이에 해당될 수 있다.
- Multivariate : <u>종속변수가 여러 개</u>인 경우. 보통 주성분분석(PCA), 요인분석(Factor analysis), 군집분석(Cluster analysis), 반복측정자료분석(Repeated measurement data analysis)등이 이에 해당한다.

<br/>

# Univariable vs Multivariable (단변수 vs 다변수)

- **독립변수 X**의 개수에 따라 구분이 가능하다.
- Univariable : <u>독립변수가 하나</u>인 경우. 
- Multivariable(or Multiple) : <u>독립변수 여러 개</u>인 경우. 하나의 종속변수에 영향을 미치는 여러 위험인자들의 영향을 서로 보정하여 각 인자들이 실제로 미치는 영향의 정도를 하나의 모형으로 설명하는 것

<br/>

### 예시

- 독립변수가 4개이고 종속변수가 1개인 회귀분석의 경우
- 독립변수 기준, Multivariable regression analysis가 된다.
- 종속변수 기준, Univariate regression analysis가 된다.



<br/>

<br/>

# 참고자료
---
[Multivariable / Multivariate regression 차이](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=missunny_&logNo=221480765144)





