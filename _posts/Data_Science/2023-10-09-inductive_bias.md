---
layout: single
title: "[기타] Inductive bias"
categories:	
    - deep learning
tags:
    - ['딥러닝', '개념']

toc: true
toc_sticky : true

author_profile: false
sidebar:
    nav: "docs"

last_modified_at:
use_math: true
---

# Inductive Bias란?

----

- 일반화 과정 중 학습 시 만나보지 못했던 상황에 대해 <u>정확한(혹은 일반적인) 예측을 하기 위해 사용하는 추가적인 가정(additional assumptions)을 의미함.</u>
- 일반적으로 모델이 갖는 일반화의 오류(Generalization Problem)는 다음으로 나뉨.
  - Models are <font color='blue'>brittle</font> : 데이터의 Input이 조금만 바뀌어도 모델의 결과가 망가지게 되는 것. (불안정)
  - Models are <font color='blue'> spurious</font> : 데이터 본연의 의미를 학습한 것이 아닌 결과(artifacts)와 편향(biases)를 학습하는 것. (겉으로만 그럴싸하게 만듦)
- 모델 학습 과정에서 학습 데이터 이외의 새로운 데이터에 대해서도 정확한 예측을 위해선 <u>추가적인 가정이 필요한데, inductive bias는 이때 필요로 하는 것임.</u> (일반화가 잘 된 모델들은 어떠한 inductive bias를 가졌다고도 말할 수 있다.)



<br>

# 머신러닝에서 Bias와 Variance의 의미

----

![img2](https://velog.velcdn.com/images%2Feuisuk-chung%2Fpost%2F7ca41fb6-c4a9-4348-8832-3fb3322f8055%2Fbias_variance.png)

- Error는 다음으로 나뉨.
  - Bias(의 제곱) : 실제값($\hat{F}^{*}(X_{0})$)과 예측값들의 평균($\bar{F}(X_{0})$)의 차이를 의미.
    - 낮은 Bias는 실제값에 근사하게 예측을 수행하는 것이며, 높은 Bias는 평균이 멀리 떨어져 있음으로 poor match를 의미.
    - 모델의 예측값과 실제값이 얼마나 떨어져있는가를 의미하며, Bias가 크면 과소적합(underfitting)을 야기함.
  - Variance : 예측값들의 평균($\bar{F}^{*}(X_{0})$)으로부터 특정 예측값들($\hat{F}(X_{0})$)이 어느 정도 퍼져있는 가를 의미.
    - 낮은 Variance는 예측값들이 크게 변동이 없다는 것을 의미하며, 높은 variance는 큰 변동이 있으므로 poor match를 의미.
    - 모델의 복잡도로 해석할 수 있으며, Variance가 크면 일반화 되지 않은 과대적합(overfitting)을 야기함.

![img1](https://velog.velcdn.com/images%2Feuisuk-chung%2Fpost%2F487edc3f-7a63-4262-909b-6b6d0b59a112%2FError.png)

- 현실적으로 Bias와 Variance를 모두 낮추는 것은 거의 불가능. (Trade-off 관계)
  - 이는 bias-variance trade-off 라고 불림.



<br>

# Inductive Bias의 가정들

---

- 위에서 Inductive Bias는 추가적인 가정이 필요하다고 했음. 그 가정은 그게 다음으로 나뉨.
  - <u>Relational</u> : 입력 요소(element)와 출력 요소(element)의 관계에 초점을 맞춘 것을 의미. (눈여겨 봐야할 부분.)
  - Non-relational

- 모델의 형태에 따라 적합한 Inductive Bias를 갖고 있음.

![img4](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcvfpTn%2FbtrtYcDYZm9%2FTWXnuk8rxrAh6BjUhqNE91%2Fimg.png)

## FCN vs CNN

- FCN(Fully Connected Neural Network)은 가장 일반적인 블록의 형태.
  - 가중치와 편향으로 각 층의 요소들이 서로 모두 연결되어 있음.
  - 즉, 모든 입력의 요소가 어떤 출력 요소던지 영향을 미칠 수 있기 때문에, Inductive Bias가 매우 약함.
- CNN(Convolutional Neural Network)은 Convolution Filter가 입력을 Window Silding함. (이미지 처리에서 주로 쓰임.)
  - CNN의 Entities(Grid Elements, Pixels)는 FCN의 Entities(Elements)와 마찬가지로 개별단위이지만, Entities 간의 관계가 약하다는 차이점이 있음.
  - Locality & Translation Invariance의 Relational Inductive Biases를 갖음.
    - Locality : 입력에서 각 Entities 간의 관계가 서로 가까운 요소들에 존재한다는 것을 의미.
    - Translation Invariance : 입력과 동일하게 계속해서 관계가 유지된다는 것을 의미.
    - 이는 어떤 특징을 가지는 요소들이 서로 모여있는지가 중요한 문제에서 좋은 성능을 보여준다는 것을 의미.



## CNN vs RNN vs GNN

- CNN은 공간의 개념을, RNN은 시간의 개념을, GNN은 그래프의 개념을 사용함.
- CNN의 Locality & Translation Invariance와 유사한 개념으로 RNN(Reccurent Neural Network)과 GNN(Graph Neural Network)은 다음과 Bias를 갖음.
  - RNN : Sequential & Temporal Invariance의 Relational Inductive Biases.
    - Sequential : 시계열의 특징을 갖는다고 가정한 입력.
    - Temporal Invariance : 동일한 순서로 입력이 들어오면 출력도 동일함.
  - GNN : Permutational Invarianced의 Relational Inductive Biases.

- 이러한 가정들의 장점은 가정이 맞는 경우에 좋은 성능을 보여주지만, 가정이 맞지 않으면 매우 약한 모습을 보여준다는 것임.



## CNN vs Transformer

- Transformer는 NLP 분야 뿐만 아니라, 최근 CV(Computer Vision)에서도 강력한 성능을 보여줌.
- 그렇다면, 항상 Transformer를 사용하는 것이 좋으냐?
  - CNN은 이미지가 지역적으로 얻을 정보가 많다는 것을 가정함.
  - 반면, Transformer는 Positional Embedding과 Self-Attention을 사용하여 모든 정보를 활용함.
  - 즉, Global한 정보가 필요한 경우 Transformer가 좋지만, Inductive Biases가 잘 맞는 이미지가 지역적으로 얻을 정보가 많은 경우엔 CNN이 적합하다는 것을 알 수 있음.





<br><br>

# 참고자료

---

- [https://darkpgmr.tistory.com/153](https://darkpgmr.tistory.com/153)
- [https://velog.io/@euisuk-chung/Inductive-Bias%EB%9E%80](https://velog.io/@euisuk-chung/Inductive-Bias%EB%9E%80)
