---
layout: single
title: "Pytorch GPU 사용법"
categories:	
    - competition reference
tags:
    - ['캐글', '대회', '필사', '설치']

toc: true
toc_sticky : true

author_profile: false
sidebar:
    nav: "docs"

last_modified_at:
---


# pytorch GPU 튜토리얼
## GPU가 사용 가능한지 확인


```python
import torch

torch.cuda.is_available() # 기본 CPU이기 때문에 실패
```




    True




```python
import torch

# ... 메뉴로 이동해서 Accelerator - GPU T4 x2로 변경한다.
torch.cuda.is_available() 
```




    True



기본적으로 생성된 변수는 모두 CPU에 존재한다.


```python
list0 = [1,2,3,4]
x = torch.tensor(list0)
x.is_cuda
```




    False




```python
x = x.to('cuda')
x.is_cuda
```




    True



다음 코드를 cuda로 실행할 수 있도록 수정해보자.


```python
import torch
from torch import nn
from torch.utils.data import DataLoader
from torchvision import datasets
from torchvision.transforms import ToTensor
import matplotlib.pyplot as plt
import matplotlib.cm as cm
import numpy as np
import os 

# 트레이닝 데이터셋을 다운로드한다.
training_data = datasets.FashionMNIST(
    root="data",
    train=True,
    download=True,
    transform=ToTensor()
)

# 테스트 데이터셋을 다운로드한다.
test_data = datasets.FashionMNIST(
    root="data",
    train=False,
    download=True,
    transform=ToTensor()
)

train_dataloader = DataLoader(training_data, batch_size=64)
test_dataloader = DataLoader(test_data, batch_size=64)

class DNN(nn.Module):
    def __init__(self):
        super(DNN, self).__init__()
        self.linear_relu_stack = nn.Sequential(
            nn.Flatten(),
            nn.Linear(784, 512),
            nn.ReLU(),
            nn.Linear(512, 512),
            nn.ReLU(),
            nn.Linear(512, 10),
        )
        
    # 포워드 패스    
    def forward(self, x):
        return self.linear_relu_stack(x)


```

    Downloading http://fashion-mnist.s3-website.eu-central-1.amazonaws.com/train-images-idx3-ubyte.gz
    Downloading http://fashion-mnist.s3-website.eu-central-1.amazonaws.com/train-images-idx3-ubyte.gz to data/FashionMNIST/raw/train-images-idx3-ubyte.gz



      0%|          | 0/26421880 [00:00<?, ?it/s]


    Extracting data/FashionMNIST/raw/train-images-idx3-ubyte.gz to data/FashionMNIST/raw
    
    Downloading http://fashion-mnist.s3-website.eu-central-1.amazonaws.com/train-labels-idx1-ubyte.gz
    Downloading http://fashion-mnist.s3-website.eu-central-1.amazonaws.com/train-labels-idx1-ubyte.gz to data/FashionMNIST/raw/train-labels-idx1-ubyte.gz



      0%|          | 0/29515 [00:00<?, ?it/s]


    Extracting data/FashionMNIST/raw/train-labels-idx1-ubyte.gz to data/FashionMNIST/raw
    
    Downloading http://fashion-mnist.s3-website.eu-central-1.amazonaws.com/t10k-images-idx3-ubyte.gz
    Downloading http://fashion-mnist.s3-website.eu-central-1.amazonaws.com/t10k-images-idx3-ubyte.gz to data/FashionMNIST/raw/t10k-images-idx3-ubyte.gz



      0%|          | 0/4422102 [00:00<?, ?it/s]


    Extracting data/FashionMNIST/raw/t10k-images-idx3-ubyte.gz to data/FashionMNIST/raw
    
    Downloading http://fashion-mnist.s3-website.eu-central-1.amazonaws.com/t10k-labels-idx1-ubyte.gz
    Downloading http://fashion-mnist.s3-website.eu-central-1.amazonaws.com/t10k-labels-idx1-ubyte.gz to data/FashionMNIST/raw/t10k-labels-idx1-ubyte.gz



      0%|          | 0/5148 [00:00<?, ?it/s]


    Extracting data/FashionMNIST/raw/t10k-labels-idx1-ubyte.gz to data/FashionMNIST/raw
    



```python




def train_loop(dataloader, model, loss_fn, optimzer, device):
    size = len(dataloader.dataset)
    model.train() # 모델을 훈련 모드로 설정
    
    for batch, (X, y) in enumerate(dataloader):
        X = X.to(device)
        y = y.to(device)
        pred = model(X) # 포워드 패스 수행
        loss = loss_fn(pred, y) # CE 연산
        
        optimzer.zero_grad() # 0 으로 초기화
        loss.backward() # 역전파하여 그래디언트 계산
        optimzer.step() # 연산된 그래디언트를 사용해 파라미터를 업데이트
        
        if batch % 100 == 0: # 매 100회차 마다 다음 내용 출력
            loss, current = loss.item(), batch * len(X)
            #print(f'loss: {loss}, [{current:>5d}/{size:>5d}]')

def test_loop(dataloader, model, loss_fn, device):
    size = len(dataloader.dataset)
    num_batches = len(dataloader)
    test_loss, correct = 0, 0
    model.eval() # 모델을 실행 모드로 설정
    
    with torch.no_grad(): # 그래디언트 연산 안함
        for X, y in dataloader:
            X = X.to(device)
            y = y.to(device)
            
            pred = model(X) # 포워드 패스 수행
            test_loss += loss_fn(pred, y) # CE 연산
            correct += (pred.argmax(1) == y).type(torch.float).sum().item() # 결과 일치하는지 확인
    
    test_loss /= num_batches
    correct /= size
    print(f'Test Error: \n 정확도: {(100*correct):>0.1f}% 평균 Loss: {test_loss:>8f}\n')
    

```


```python
def run(device):
    #device = 'cuda:0' if torch.cuda.is_available() else 'cpu'   
    #device = 'cpu'
    print(f"사용할 장치: {device}")

    model = DNN().to(device)

    learning_rate = 1e-3
    batch_size = 64
    epochs = 10


    loss_fn = nn.CrossEntropyLoss().to(device)
    optimizer = torch.optim.SGD(model.parameters(), lr=learning_rate)

    for t in range(epochs):
        print(f'Epoch {t+1}\n--------------------------------------')
        train_loop(train_dataloader, model, loss_fn, optimizer, device)
        test_loop(train_dataloader, model, loss_fn, device)
    print("Done!")
```


```python
%timeit run('cpu')
```

    사용할 장치: cpu
    Epoch 1
    --------------------------------------
    Test Error: 
     정확도: 44.5% 평균 Loss: 2.145079
    
    Epoch 2
    --------------------------------------
    Test Error: 
     정확도: 57.3% 평균 Loss: 1.859738
    
    Epoch 3
    --------------------------------------
    Test Error: 
     정확도: 62.1% 평균 Loss: 1.477654
    
    Epoch 4
    --------------------------------------
    Test Error: 
     정확도: 63.9% 평균 Loss: 1.217386
    
    Epoch 5
    --------------------------------------
    Test Error: 
     정확도: 65.6% 평균 Loss: 1.062857
    
    Epoch 6
    --------------------------------------
    Test Error: 
     정확도: 67.0% 평균 Loss: 0.964224
    
    Epoch 7
    --------------------------------------
    Test Error: 
     정확도: 68.2% 평균 Loss: 0.895859
    
    Epoch 8
    --------------------------------------
    Test Error: 
     정확도: 69.3% 평균 Loss: 0.845354
    
    Epoch 9
    --------------------------------------
    Test Error: 
     정확도: 70.5% 평균 Loss: 0.806216
    
    Epoch 10
    --------------------------------------
    Test Error: 
     정확도: 71.8% 평균 Loss: 0.774687
    
    Done!
    사용할 장치: cpu
    Epoch 1
    --------------------------------------
    Test Error: 
     정확도: 46.2% 평균 Loss: 2.149195
    
    Epoch 2
    --------------------------------------
    Test Error: 
     정확도: 58.6% 평균 Loss: 1.876602
    
    Epoch 3
    --------------------------------------
    Test Error: 
     정확도: 62.0% 평균 Loss: 1.500106
    
    Epoch 4
    --------------------------------------
    Test Error: 
     정확도: 64.6% 평균 Loss: 1.226242
    
    Epoch 5
    --------------------------------------
    Test Error: 
     정확도: 66.0% 평균 Loss: 1.061437
    
    Epoch 6
    --------------------------------------
    Test Error: 
     정확도: 67.4% 평균 Loss: 0.957544
    
    Epoch 7
    --------------------------------------
    Test Error: 
     정확도: 68.8% 평균 Loss: 0.887291
    
    Epoch 8
    --------------------------------------
    Test Error: 
     정확도: 69.9% 평균 Loss: 0.836570
    
    Epoch 9
    --------------------------------------
    Test Error: 
     정확도: 71.1% 평균 Loss: 0.797968
    
    Epoch 10
    --------------------------------------
    Test Error: 
     정확도: 72.3% 평균 Loss: 0.767037
    
    Done!
    사용할 장치: cpu
    Epoch 1
    --------------------------------------
    Test Error: 
     정확도: 41.7% 평균 Loss: 2.130327
    
    Epoch 2
    --------------------------------------
    Test Error: 
     정확도: 57.1% 평균 Loss: 1.842872
    
    Epoch 3
    --------------------------------------
    Test Error: 
     정확도: 61.3% 평균 Loss: 1.480659
    
    Epoch 4
    --------------------------------------
    Test Error: 
     정확도: 63.9% 평균 Loss: 1.227481
    
    Epoch 5
    --------------------------------------
    Test Error: 
     정확도: 65.6% 평균 Loss: 1.070095
    
    Epoch 6
    --------------------------------------
    Test Error: 
     정확도: 67.1% 평균 Loss: 0.967320
    
    Epoch 7
    --------------------------------------
    Test Error: 
     정확도: 68.6% 평균 Loss: 0.896083
    
    Epoch 8
    --------------------------------------
    Test Error: 
     정확도: 69.8% 평균 Loss: 0.843942
    
    Epoch 9
    --------------------------------------
    Test Error: 
     정확도: 71.0% 평균 Loss: 0.803888
    
    Epoch 10
    --------------------------------------
    Test Error: 
     정확도: 72.3% 평균 Loss: 0.771779
    
    Done!
    사용할 장치: cpu
    Epoch 1
    --------------------------------------
    Test Error: 
     정확도: 43.6% 평균 Loss: 2.148021
    
    Epoch 2
    --------------------------------------
    Test Error: 
     정확도: 57.7% 평균 Loss: 1.868261
    
    Epoch 3
    --------------------------------------
    Test Error: 
     정확도: 61.5% 평균 Loss: 1.504941
    
    Epoch 4
    --------------------------------------
    Test Error: 
     정확도: 64.1% 평균 Loss: 1.248063
    
    Epoch 5
    --------------------------------------
    Test Error: 
     정확도: 66.0% 평균 Loss: 1.086045
    
    Epoch 6
    --------------------------------------
    Test Error: 
     정확도: 67.4% 평균 Loss: 0.979198
    
    Epoch 7
    --------------------------------------
    Test Error: 
     정확도: 68.6% 평균 Loss: 0.904965
    
    Epoch 8
    --------------------------------------
    Test Error: 
     정확도: 69.7% 평균 Loss: 0.850483
    
    Epoch 9
    --------------------------------------
    Test Error: 
     정확도: 70.7% 평균 Loss: 0.808730
    
    Epoch 10
    --------------------------------------
    Test Error: 
     정확도: 71.8% 평균 Loss: 0.775525
    
    Done!
    사용할 장치: cpu
    Epoch 1
    --------------------------------------
    Test Error: 
     정확도: 48.0% 평균 Loss: 2.155469
    
    Epoch 2
    --------------------------------------
    Test Error: 
     정확도: 59.7% 평균 Loss: 1.883491
    
    Epoch 3
    --------------------------------------
    Test Error: 
     정확도: 63.8% 평균 Loss: 1.507828
    
    Epoch 4
    --------------------------------------
    Test Error: 
     정확도: 64.8% 평균 Loss: 1.237764
    
    Epoch 5
    --------------------------------------
    Test Error: 
     정확도: 66.1% 평균 Loss: 1.072880
    
    Epoch 6
    --------------------------------------
    Test Error: 
     정확도: 67.5% 평균 Loss: 0.967337
    
    Epoch 7
    --------------------------------------
    Test Error: 
     정확도: 68.8% 평균 Loss: 0.895187
    
    Epoch 8
    --------------------------------------
    Test Error: 
     정확도: 70.0% 평균 Loss: 0.842927
    
    Epoch 9
    --------------------------------------
    Test Error: 
     정확도: 71.1% 평균 Loss: 0.802981
    
    Epoch 10
    --------------------------------------
    Test Error: 
     정확도: 72.2% 평균 Loss: 0.771037
    
    Done!
    사용할 장치: cpu
    Epoch 1
    --------------------------------------
    Test Error: 
     정확도: 43.3% 평균 Loss: 2.151541
    
    Epoch 2
    --------------------------------------
    Test Error: 
     정확도: 55.8% 평균 Loss: 1.879372
    
    Epoch 3
    --------------------------------------
    Test Error: 
     정확도: 60.4% 평균 Loss: 1.515021
    
    Epoch 4
    --------------------------------------
    Test Error: 
     정확도: 63.5% 평균 Loss: 1.258509
    
    Epoch 5
    --------------------------------------
    Test Error: 
     정확도: 65.3% 평균 Loss: 1.094885
    
    Epoch 6
    --------------------------------------
    Test Error: 
     정확도: 66.8% 평균 Loss: 0.985699
    
    Epoch 7
    --------------------------------------
    Test Error: 
     정확도: 68.1% 평균 Loss: 0.910132
    
    Epoch 8
    --------------------------------------
    Test Error: 
     정확도: 69.3% 평균 Loss: 0.855560
    
    Epoch 9
    --------------------------------------
    Test Error: 
     정확도: 70.6% 평균 Loss: 0.814243
    
    Epoch 10
    --------------------------------------
    Test Error: 
     정확도: 71.7% 평균 Loss: 0.781519
    
    Done!
    사용할 장치: cpu
    Epoch 1
    --------------------------------------
    Test Error: 
     정확도: 47.6% 평균 Loss: 2.164351
    
    Epoch 2
    --------------------------------------
    Test Error: 
     정확도: 61.6% 평균 Loss: 1.895376
    
    Epoch 3
    --------------------------------------
    Test Error: 
     정확도: 61.8% 평균 Loss: 1.531638
    
    Epoch 4
    --------------------------------------
    Test Error: 
     정확도: 63.8% 평균 Loss: 1.265460
    
    Epoch 5
    --------------------------------------
    Test Error: 
     정확도: 65.5% 평균 Loss: 1.096596
    
    Epoch 6
    --------------------------------------
    Test Error: 
     정확도: 66.9% 평균 Loss: 0.985845
    
    Epoch 7
    --------------------------------------
    Test Error: 
     정확도: 68.2% 평균 Loss: 0.909336
    
    Epoch 8
    --------------------------------------
    Test Error: 
     정확도: 69.6% 평균 Loss: 0.853740
    
    Epoch 9
    --------------------------------------
    Test Error: 
     정확도: 70.8% 평균 Loss: 0.811476
    
    Epoch 10
    --------------------------------------
    Test Error: 
     정확도: 71.9% 평균 Loss: 0.778093
    
    Done!
    사용할 장치: cpu
    Epoch 1
    --------------------------------------
    Test Error: 
     정확도: 21.1% 평균 Loss: 2.166270
    
    Epoch 2
    --------------------------------------
    Test Error: 
     정확도: 51.2% 평균 Loss: 1.913578
    
    Epoch 3
    --------------------------------------
    Test Error: 
     정확도: 58.8% 평균 Loss: 1.542653
    
    Epoch 4
    --------------------------------------
    Test Error: 
     정확도: 63.4% 평균 Loss: 1.257574
    
    Epoch 5
    --------------------------------------
    Test Error: 
     정확도: 65.3% 평균 Loss: 1.082608
    
    Epoch 6
    --------------------------------------
    Test Error: 
     정확도: 66.8% 평균 Loss: 0.971342
    
    Epoch 7
    --------------------------------------
    Test Error: 
     정확도: 68.2% 평균 Loss: 0.896464
    
    Epoch 8
    --------------------------------------
    Test Error: 
     정확도: 69.3% 평균 Loss: 0.843298
    
    Epoch 9
    --------------------------------------
    Test Error: 
     정확도: 70.5% 평균 Loss: 0.803402
    
    Epoch 10
    --------------------------------------
    Test Error: 
     정확도: 71.8% 평균 Loss: 0.771842
    
    Done!
    2min 42s ± 1.13 s per loop (mean ± std. dev. of 7 runs, 1 loop each)



```python
%timeit run('cuda:0')
```

    사용할 장치: cuda:0
    Epoch 1
    --------------------------------------
    Test Error: 
     정확도: 51.8% 평균 Loss: 2.140260
    
    Epoch 2
    --------------------------------------
    Test Error: 
     정확도: 58.2% 평균 Loss: 1.856035
    
    Epoch 3
    --------------------------------------
    Test Error: 
     정확도: 61.2% 평균 Loss: 1.494435
    
    Epoch 4
    --------------------------------------
    Test Error: 
     정확도: 64.2% 평균 Loss: 1.241039
    
    Epoch 5
    --------------------------------------
    Test Error: 
     정확도: 65.8% 평균 Loss: 1.080358
    
    Epoch 6
    --------------------------------------
    Test Error: 
     정확도: 67.2% 평균 Loss: 0.973863
    
    Epoch 7
    --------------------------------------
    Test Error: 
     정확도: 68.5% 평균 Loss: 0.899747
    
    Epoch 8
    --------------------------------------
    Test Error: 
     정확도: 69.8% 평균 Loss: 0.845576
    
    Epoch 9
    --------------------------------------
    Test Error: 
     정확도: 71.1% 평균 Loss: 0.804218
    
    Epoch 10
    --------------------------------------
    Test Error: 
     정확도: 72.4% 평균 Loss: 0.771310
    
    Done!
    사용할 장치: cuda:0
    Epoch 1
    --------------------------------------
    Test Error: 
     정확도: 54.1% 평균 Loss: 2.158655
    
    Epoch 2
    --------------------------------------
    Test Error: 
     정확도: 63.4% 평균 Loss: 1.874954
    
    Epoch 3
    --------------------------------------
    Test Error: 
     정확도: 63.4% 평균 Loss: 1.488790
    
    Epoch 4
    --------------------------------------
    Test Error: 
     정확도: 64.3% 평균 Loss: 1.222355
    
    Epoch 5
    --------------------------------------
    Test Error: 
     정확도: 65.7% 평균 Loss: 1.062467
    
    Epoch 6
    --------------------------------------
    Test Error: 
     정확도: 67.0% 평균 Loss: 0.960324
    
    Epoch 7
    --------------------------------------
    Test Error: 
     정확도: 68.1% 평균 Loss: 0.890408
    
    Epoch 8
    --------------------------------------
    Test Error: 
     정확도: 69.3% 평균 Loss: 0.839719
    
    Epoch 9
    --------------------------------------
    Test Error: 
     정확도: 70.5% 평균 Loss: 0.801025
    
    Epoch 10
    --------------------------------------
    Test Error: 
     정확도: 71.8% 평균 Loss: 0.770026
    
    Done!
    사용할 장치: cuda:0
    Epoch 1
    --------------------------------------
    Test Error: 
     정확도: 44.4% 평균 Loss: 2.158776
    
    Epoch 2
    --------------------------------------
    Test Error: 
     정확도: 56.0% 평균 Loss: 1.890853
    
    Epoch 3
    --------------------------------------
    Test Error: 
     정확도: 60.0% 평균 Loss: 1.520205
    
    Epoch 4
    --------------------------------------
    Test Error: 
     정확도: 63.2% 평균 Loss: 1.256206
    
    Epoch 5
    --------------------------------------
    Test Error: 
     정확도: 65.1% 평균 Loss: 1.092746
    
    Epoch 6
    --------------------------------------
    Test Error: 
     정확도: 66.5% 평균 Loss: 0.985381
    
    Epoch 7
    --------------------------------------
    Test Error: 
     정확도: 67.8% 평균 Loss: 0.911010
    
    Epoch 8
    --------------------------------------
    Test Error: 
     정확도: 69.0% 평균 Loss: 0.856955
    
    Epoch 9
    --------------------------------------
    Test Error: 
     정확도: 70.1% 평균 Loss: 0.815837
    
    Epoch 10
    --------------------------------------
    Test Error: 
     정확도: 71.4% 평균 Loss: 0.783171
    
    Done!
    사용할 장치: cuda:0
    Epoch 1
    --------------------------------------
    Test Error: 
     정확도: 46.0% 평균 Loss: 2.148222
    
    Epoch 2
    --------------------------------------
    Test Error: 
     정확도: 57.1% 평균 Loss: 1.856956
    
    Epoch 3
    --------------------------------------
    Test Error: 
     정확도: 62.2% 평균 Loss: 1.486109
    
    Epoch 4
    --------------------------------------
    Test Error: 
     정확도: 64.8% 평균 Loss: 1.230731
    
    Epoch 5
    --------------------------------------
    Test Error: 
     정확도: 66.1% 평균 Loss: 1.069811
    
    Epoch 6
    --------------------------------------
    Test Error: 
     정확도: 67.4% 평균 Loss: 0.963705
    
    Epoch 7
    --------------------------------------
    Test Error: 
     정확도: 68.7% 평균 Loss: 0.890600
    
    Epoch 8
    --------------------------------------
    Test Error: 
     정확도: 69.9% 평균 Loss: 0.837781
    
    Epoch 9
    --------------------------------------
    Test Error: 
     정확도: 71.2% 평균 Loss: 0.797687
    
    Epoch 10
    --------------------------------------
    Test Error: 
     정확도: 72.5% 평균 Loss: 0.765782
    
    Done!
    사용할 장치: cuda:0
    Epoch 1
    --------------------------------------
    Test Error: 
     정확도: 47.2% 평균 Loss: 2.147807
    
    Epoch 2
    --------------------------------------
    Test Error: 
     정확도: 60.2% 평균 Loss: 1.871584
    
    Epoch 3
    --------------------------------------
    Test Error: 
     정확도: 64.1% 평균 Loss: 1.501724
    
    Epoch 4
    --------------------------------------
    Test Error: 
     정확도: 64.7% 평균 Loss: 1.233301
    
    Epoch 5
    --------------------------------------
    Test Error: 
     정확도: 65.8% 평균 Loss: 1.069727
    
    Epoch 6
    --------------------------------------
    Test Error: 
     정확도: 67.1% 평균 Loss: 0.965442
    
    Epoch 7
    --------------------------------------
    Test Error: 
     정확도: 68.4% 평균 Loss: 0.893907
    
    Epoch 8
    --------------------------------------
    Test Error: 
     정확도: 69.8% 평균 Loss: 0.841642
    
    Epoch 9
    --------------------------------------
    Test Error: 
     정확도: 71.1% 평균 Loss: 0.801505
    
    Epoch 10
    --------------------------------------
    Test Error: 
     정확도: 72.3% 평균 Loss: 0.769341
    
    Done!
    사용할 장치: cuda:0
    Epoch 1
    --------------------------------------
    Test Error: 
     정확도: 55.7% 평균 Loss: 2.161969
    
    Epoch 2
    --------------------------------------
    Test Error: 
     정확도: 59.0% 평균 Loss: 1.905919
    
    Epoch 3
    --------------------------------------
    Test Error: 
     정확도: 61.4% 평균 Loss: 1.528095
    
    Epoch 4
    --------------------------------------
    Test Error: 
     정확도: 64.1% 평균 Loss: 1.250129
    
    Epoch 5
    --------------------------------------
    Test Error: 
     정확도: 65.9% 평균 Loss: 1.078414
    
    Epoch 6
    --------------------------------------
    Test Error: 
     정확도: 67.3% 평균 Loss: 0.967924
    
    Epoch 7
    --------------------------------------
    Test Error: 
     정확도: 68.6% 평균 Loss: 0.892969
    
    Epoch 8
    --------------------------------------
    Test Error: 
     정확도: 69.8% 평균 Loss: 0.839180
    
    Epoch 9
    --------------------------------------
    Test Error: 
     정확도: 71.0% 평균 Loss: 0.798609
    
    Epoch 10
    --------------------------------------
    Test Error: 
     정확도: 72.3% 평균 Loss: 0.766456
    
    Done!
    사용할 장치: cuda:0
    Epoch 1
    --------------------------------------
    Test Error: 
     정확도: 49.5% 평균 Loss: 2.137333
    
    Epoch 2
    --------------------------------------
    Test Error: 
     정확도: 57.5% 평균 Loss: 1.838759
    
    Epoch 3
    --------------------------------------
    Test Error: 
     정확도: 63.2% 평균 Loss: 1.474286
    
    Epoch 4
    --------------------------------------
    Test Error: 
     정확도: 65.2% 평균 Loss: 1.221576
    
    Epoch 5
    --------------------------------------
    Test Error: 
     정확도: 66.3% 평균 Loss: 1.061860
    
    Epoch 6
    --------------------------------------
    Test Error: 
     정확도: 67.4% 평균 Loss: 0.957578
    
    Epoch 7
    --------------------------------------
    Test Error: 
     정확도: 68.7% 평균 Loss: 0.886144
    
    Epoch 8
    --------------------------------------
    Test Error: 
     정확도: 70.0% 평균 Loss: 0.834677
    
    Epoch 9
    --------------------------------------
    Test Error: 
     정확도: 71.2% 평균 Loss: 0.795693
    
    Epoch 10
    --------------------------------------
    Test Error: 
     정확도: 72.4% 평균 Loss: 0.764703
    
    Done!
    사용할 장치: cuda:0
    Epoch 1
    --------------------------------------
    Test Error: 
     정확도: 46.9% 평균 Loss: 2.151836
    
    Epoch 2
    --------------------------------------
    Test Error: 
     정확도: 58.4% 평균 Loss: 1.881667
    
    Epoch 3
    --------------------------------------
    Test Error: 
     정확도: 61.5% 평균 Loss: 1.508986
    
    Epoch 4
    --------------------------------------
    Test Error: 
     정확도: 64.5% 평균 Loss: 1.242306
    
    Epoch 5
    --------------------------------------
    Test Error: 
     정확도: 66.3% 평균 Loss: 1.076750
    
    Epoch 6
    --------------------------------------
    Test Error: 
     정확도: 67.7% 평균 Loss: 0.967935
    
    Epoch 7
    --------------------------------------
    Test Error: 
     정확도: 69.1% 평균 Loss: 0.893048
    
    Epoch 8
    --------------------------------------
    Test Error: 
     정확도: 70.4% 평균 Loss: 0.838853
    
    Epoch 9
    --------------------------------------
    Test Error: 
     정확도: 71.6% 평균 Loss: 0.797739
    
    Epoch 10
    --------------------------------------
    Test Error: 
     정확도: 72.9% 평균 Loss: 0.765048
    
    Done!
    2min 12s ± 859 ms per loop (mean ± std. dev. of 7 runs, 1 loop each)



```python

```


```python

```
<br/>

<br/>

# 참고자료
---
[Pytorch-GPU 사용](https://www.kaggle.com/code/isc0304/pytorch-gpu)