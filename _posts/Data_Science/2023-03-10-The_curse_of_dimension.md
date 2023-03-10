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

## 차원의 저주

- 데이터 학습을 위해 <span style='color: red'>차원이 증가</span>하면서 학습데이터 수가 차원의 수보다 적어져 <span style='color: red'>성능이 저하되는 현상</span>
- 차원이 증가할수록 변수가 증가하고, 개별 차원 내에서 학습할 데이터 수가 적어짐
- 일반적으로 **관측치**보다 변수 수가 많아지는 경우 발생
- 아래 그림과 같이 차원이 증가할수록 **빈 공간**이 많아짐

![image](https://blog.kakaocdn.net/dn/6g8f5/btqv0yFthYE/NqRQzG0fTTuhYxeCJe865k/img.png)

*차원의 저주(The curse of dimension)*



---

# 참고 자료

- [차원의 저주 (The curse of dimensionality)](https://datapedia.tistory.com/15)