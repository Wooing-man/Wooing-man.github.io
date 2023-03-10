---
layout: single
title: "[차원 축소] 차원축소 방법 종류별 비교"
categories:	
    - dimensionality reduction
tags:
    - ['차원축소', '데이터 전처리', '데이터 엔지니어링', '알고리즘']

toc: true
toc_sticky : true

author_profile: false
sidebar:
    nav: "docs"

last_modified_at:
---

# 차원 축소를 위한 알고리즘 : PCA, T-SNE, UMAP

----

# 차원 축소란?

- **고차원 데이터에서 저차원 데이터로 변환하는 방법** 또는 **매우 많은 특징으로 구성된 다차원 데이터 세트의 차원을 축소해 새로운 차원의 데이터를 생성 하는 과정**
- 일반적으로 차원이 증가할 경우 데이터 간 거리가 기하급수적으로 증가하기 때문에 희소한 구조를 가지게 되어 모델의 예측 신뢰도가 떨어지게 됨
- 일반적으로 차원 축소는 **특징(Feature)**에 대해 **선택(Selection)**과 **추출(Extraction)**으로 나눌 수 있음
  1. 특징 선택(Feature Selection) : 종속성이 강한 불필요한 특징을 제거하는 방법
  2. 특징 추출(Feature Extraction) : 기존 특징에서 더 저차원으로 중요 정보만갖도록 특징으로 압축하는 방법

- 결론적으로 차원 축소를 통해 데이터를 잘 설명할 수 있는 잠재적 요소를 나타내고자 하는 것 (모델 성능에 영향)

<br/>

 <center><u>이번 포스팅에는 특징 추출에 대해서만 다루고 있음</u><center>
<br/>



# 차원 축소 알고리즘 (Dimension reduction algorithm)

- 차원 축소 알고리즘은 아래와 같이 2가지의 종류로 나뉘며 포함하는 알고리즘은 다음과 같음
  - Metrix factorization
    - PCA (prinsipal componant analysis)
  - Neightbor graph
    - T-SNE (t-distributed Stochastic Neighbor Embedding)
    - UMAP (Uniform Manifold Approximation and Projection)



## PCA

- 









---

# 참고 자료

- [위키백과 PCA편](https://ko.wikipedia.org/wiki/%EC%B0%A8%EC%9B%90_%EC%B6%95%EC%86%8C_(%ED%86%B5%EA%B3%84%ED%95%99))
- [차원 축소 (Dimension Reduction) - PCA, LDA]( https://casa-de-feel.tistory.com/19)
- [차원 축소 알고리즘을 비교해보자 (PCA, T-sne, UMAP)](https://velog.io/@stella_y/%EC%B0%A8%EC%9B%90-%EC%B6%95%EC%86%8C-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98%EC%9D%84-%EB%B9%84%EA%B5%90%ED%95%B4%EB%B3%B4%EC%9E%90-PCA-T-sne-UMAP)

