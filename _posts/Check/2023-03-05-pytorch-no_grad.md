---
layout: single
title: "[Pytorch] no_grad와 eval의 차이"
categories:
    - pytorch
tags: 
    - ["파이토치", "팁"]

toc: true
toc_sticky : true

author_profile: false
sidebar:
    nav: "docs"

last_modified_at:
---

<center>Pytorch에서 no_grad()와 eval()의 차이를 알아보자.</center>

---

왜 model.eval()을 선언해놓고 또 with torch.no_grad(): 로 감싸주는가?

이 둘에겐 차이가 있다.

<br/>

### with tworch.no_grad()

이와 같이 **no_grad()** with statement에 포함시키게 되면 autogrid engine을 꺼버린다. 더 이상 자동으로 gradient를 트래킹하지 않는다는 뜻이다. 여기서 의문이 들 수 있는 것이, **loss.backward()**를 통해 backpropagation을 진행하지 않는다면 gradient를 계산하든지 말든지 상관 없지 않은가?

맞는 말이지만, **torch.no_grad()**의 주된 목적은 autograd를 끔으로써 

- 메모리 사용량을 줄이고
- 연산 속도를 높이기

위함이다. 그래서 일반적으로 inference를 진행할 때는 **torch.no_grad()** with statement로 감싼다.

<br/>

### model.eval()

모델링 시 training과 inference시에 다르게 동작하는 layer가 존재한다. 예를 들어 Dropout layer는 학습할 땐 동작하지만, inference시엔 동작하지 않는다. **model.eval()**는 이런 layer들의 동작을 inference(eval) mode로 바꿔준다는 목적으로 사용된다.

<br/>

따라서 그냥 우리는 위의 두 가지 모두를 사용해야 한다는 걸로 알자!









---

### 참고자료

- [Pytorch에서 no_grad()와 eval()의 정확한 차이는 무엇일까?](https://coffeedjimmy.github.io/pytorch/2019/11/05/pytorch_nograd_vs_train_eval/)



