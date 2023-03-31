---
layout: single
title: "[App store scraper] 앱스토어 리뷰 수집하기"
categories:	
    - data collection
tags:
    - ['데이터 수집', '리뷰']

toc: true
toc_sticky : true

author_profile: false
sidebar:
    nav: "docs"

last_modified_at:
use_math: true
---
# App store review scraping

- 구글플레이스토어와 달리, 웹에서 크롤링이 불가 (RSS를 이용하여 웹크롤링 하는  방법이 있으나, 촤대 500개의 리뷰만 가지고 올 수 있음 (참고: [[리뷰 크롤링] AppStore 어플 리뷰 가져오기 3 in python(feat.RSS)](https://signing.tistory.com/51))
- 웹으로 앱스토어에 들어가보면 리뷰는 10개 밖에 안보여줌 (애플에서 일부러 막아둔듯)
- 그렇다면 **웹**이 아닌 **앱**에서 리뷰를 수집 해야하는데 이는 사실상 불가능에 가까움
- RSS 말고 웹에서 리뷰를 수집할 수 있는 또다른 방법은 **app_store_scraper**임
  - 근데 이건 뭔가 API를 call해서 불러와서 그런건지 코드 돌릴 때마다 불러오는 리뷰 개수가 다르고, RSS(최대 500개)보다는 많이 가져오지만 1000~2000개 수준에서 밖에 못가져옴


```python
!pip install app_store_scraper

import pandas as pd
import numpy as np
import json
import time
from tqdm import tqdm
from app_store_scraper import AppStore
```

    Looking in indexes: https://pypi.org/simple, https://us-python.pkg.dev/colab-wheels/public/simple/
    Collecting app_store_scraper
      Downloading app_store_scraper-0.3.5-py3-none-any.whl (8.3 kB)
    Collecting requests==2.23.0
      Downloading requests-2.23.0-py2.py3-none-any.whl (58 kB)
    [2K     [90m━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━[0m [32m58.4/58.4 KB[0m [31m1.3 MB/s[0m eta [36m0:00:00[0m
    [?25hCollecting idna<3,>=2.5
      Downloading idna-2.10-py2.py3-none-any.whl (58 kB)
    [2K     [90m━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━[0m [32m58.8/58.8 KB[0m [31m3.8 MB/s[0m eta [36m0:00:00[0m
    [?25hCollecting chardet<4,>=3.0.2
      Downloading chardet-3.0.4-py2.py3-none-any.whl (133 kB)
    [2K     [90m━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━[0m [32m133.4/133.4 KB[0m [31m4.3 MB/s[0m eta [36m0:00:00[0m
    [?25hRequirement already satisfied: certifi>=2017.4.17 in /usr/local/lib/python3.9/dist-packages (from requests==2.23.0->app_store_scraper) (2022.12.7)
    Collecting urllib3!=1.25.0,!=1.25.1,<1.26,>=1.21.1
      Downloading urllib3-1.25.11-py2.py3-none-any.whl (127 kB)
    [2K     [90m━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━[0m [32m128.0/128.0 KB[0m [31m7.1 MB/s[0m eta [36m0:00:00[0m
    [?25hInstalling collected packages: chardet, urllib3, idna, requests, app_store_scraper
      Attempting uninstall: chardet
        Found existing installation: chardet 4.0.0
        Uninstalling chardet-4.0.0:
          Successfully uninstalled chardet-4.0.0
      Attempting uninstall: urllib3
        Found existing installation: urllib3 1.26.15
        Uninstalling urllib3-1.26.15:
          Successfully uninstalled urllib3-1.26.15
      Attempting uninstall: idna
        Found existing installation: idna 3.4
        Uninstalling idna-3.4:
          Successfully uninstalled idna-3.4
      Attempting uninstall: requests
        Found existing installation: requests 2.27.1
        Uninstalling requests-2.27.1:
          Successfully uninstalled requests-2.27.1
    [31mERROR: pip's dependency resolver does not currently take into account all the packages that are installed. This behaviour is the source of the following dependency conflicts.
    yfinance 0.2.14 requires requests>=2.26, but you have requests 2.23.0 which is incompatible.
    tweepy 4.13.0 requires requests<3,>=2.27.0, but you have requests 2.23.0 which is incompatible.
    pandas-profiling 3.2.0 requires requests>=2.24.0, but you have requests 2.23.0 which is incompatible.
    google-colab 1.0.0 requires requests>=2.27.0, but you have requests 2.23.0 which is incompatible.[0m[31m
    [0mSuccessfully installed app_store_scraper-0.3.5 chardet-3.0.4 idna-2.10 requests-2.23.0 urllib3-1.25.11



```python
def Extract_reviews(app_name:str, app_id:int): 
  ott = AppStore(country='kr', app_name=app_name, app_id=app_id)
  ott.review()

  ott_reviews = pd.DataFrame(ott.reviews).sort_values('date', ascending=False).reset_index(drop=True)
  ott_reviews['ott'] = app_name
  return ott_reviews
```


```python
ott_platform = {'tiving': 400101401, 'netflix': 363590051}

app_store_reviews = pd.DataFrame()
for i in tqdm(ott_platform):
  ott_reviews = Extract_reviews(i, ott_platform[i])
  app_store_reviews= pd.concat([app_store_reviews, ott_reviews], axis=0)

print('Done..')
```

      0%|          | 0/2 [00:00<?, ?it/s]ERROR:Base:Something went wrong: HTTPSConnectionPool(host='amp-api.apps.apple.com', port=443): Max retries exceeded with url: /v1/catalog/kr/apps/400101401/reviews?l=en-GB&offset=3960&limit=20&platform=web&additionalPlatforms=appletv%2Cipad%2Ciphone%2Cmac (Caused by ResponseError('too many 429 error responses'))
     50%|█████     | 1/2 [00:57<00:57, 57.04s/it]ERROR:Base:Something went wrong: HTTPSConnectionPool(host='amp-api.apps.apple.com', port=443): Max retries exceeded with url: /v1/catalog/kr/apps/363590051/reviews?l=en-GB&offset=1080&limit=20&platform=web&additionalPlatforms=appletv%2Cipad%2Ciphone%2Cmac (Caused by ResponseError('too many 429 error responses'))
    100%|██████████| 2/2 [01:56<00:00, 58.34s/it]

    Done..


    



```python
app_store_reviews[app_store_reviews['ott'] == 'tiving']
```





  <div id="df-a9a99afc-584a-4416-ab56-9d327c42d81f">
    <div class="colab-df-container">
      <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>developerResponse</th>
      <th>review</th>
      <th>rating</th>
      <th>isEdited</th>
      <th>title</th>
      <th>userName</th>
      <th>ott</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2023-03-29 06:57:41</td>
      <td>NaN</td>
      <td>안녕하세요 저는 태국에 살고있는 6학년 학생인데 제가 어렸을때부터 진짜 엄청 좋아하...</td>
      <td>4</td>
      <td>False</td>
      <td>도와주세요 ㅜㅜ</td>
      <td>아됴우</td>
      <td>tiving</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2023-03-23 18:40:22</td>
      <td>{'id': 35578034, 'body': '나만 안되니 고객님, 안녕하세요. T...</td>
      <td>끌 수 있게 해주세요. 손만 잘못 닿으면 앞으로 뒤로 감겨서 짜증나 죽겠어요.\n그...</td>
      <td>3</td>
      <td>False</td>
      <td>제스처모드 좀</td>
      <td>나만 안되니</td>
      <td>tiving</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2023-03-23 11:46:38</td>
      <td>{'id': 35601512, 'body': '제임스봉구 고객님, 안녕하세요. TV...</td>
      <td>이제 애플티비 tvos 버전이 16.4로 정식 판업 되었으니\nOSD 버그 얼렁 고...</td>
      <td>4</td>
      <td>False</td>
      <td>TVOS 16.4 버그 수정좀</td>
      <td>제임스봉구</td>
      <td>tiving</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2023-03-22 11:56:29</td>
      <td>{'id': 35557757, 'body': '볼수가 없슈 고객님, 안녕하세요. T...</td>
      <td>한달전쯤부터 어플이 느려서 썸네일 이미지도 안떠요. 카테고리 넘어가는데도 시간 걸리...</td>
      <td>3</td>
      <td>False</td>
      <td>너무 느려서 볼수가 없어요</td>
      <td>볼수가 없슈</td>
      <td>tiving</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2023-03-22 04:02:01</td>
      <td>{'id': 35557750, 'body': '복숭아크리스탈 고객님, 안녕하세요. ...</td>
      <td>태블릿 어플로는 플레이가 안되고 휴대폰 어플에서는 되길래 큰화면으로 보려고 웹으로 ...</td>
      <td>1</td>
      <td>False</td>
      <td>어플을 잘만들던가 아니면 웹 시청을 막지를 말던가</td>
      <td>복숭아크리스탈</td>
      <td>tiving</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>3955</th>
      <td>2017-10-06 09:04:53</td>
      <td>NaN</td>
      <td>캐쉬 이용해서 영화 소장했는데 다운이 너무나 느립니다.\n그냥 재생할려니 화면이 안...</td>
      <td>1</td>
      <td>False</td>
      <td>너무너무느림.</td>
      <td>메쥬는쨩</td>
      <td>tiving</td>
    </tr>
    <tr>
      <th>3956</th>
      <td>2017-10-05 16:49:06</td>
      <td>NaN</td>
      <td>ㅠㅠㅠ와이파이가 문젠가하기엔 다른 영상 보는 어플들은 전혀 문제가 없구요.. 티빙만...</td>
      <td>2</td>
      <td>False</td>
      <td>진짜 뚝뚝 끊기는거 너무 심해요</td>
      <td>Jeocn</td>
      <td>tiving</td>
    </tr>
    <tr>
      <th>3957</th>
      <td>2017-09-25 10:59:29</td>
      <td>NaN</td>
      <td>아이폰 8년 넘게 쓰면서 리뷰 처음 작성하는데 이런 어플은 처음이네요.. 왜 자꾸 ...</td>
      <td>1</td>
      <td>False</td>
      <td>최악</td>
      <td>hyojoo kim</td>
      <td>tiving</td>
    </tr>
    <tr>
      <th>3958</th>
      <td>2017-09-20 04:47:48</td>
      <td>NaN</td>
      <td>재밌는 게 많네요</td>
      <td>5</td>
      <td>False</td>
      <td>좋아요</td>
      <td>흑흑ㅁㅁ</td>
      <td>tiving</td>
    </tr>
    <tr>
      <th>3959</th>
      <td>2017-09-17 07:14:19</td>
      <td>NaN</td>
      <td>리얼타임방송도 아니고 VOD보는데 이렇게 자꾸 끊기면 도대체 요금은 왜 받는겁니까....</td>
      <td>1</td>
      <td>False</td>
      <td>버그수정해도 버퍼링 여전</td>
      <td>복근이짱</td>
      <td>tiving</td>
    </tr>
  </tbody>
</table>
<p>3960 rows × 8 columns</p>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-a9a99afc-584a-4416-ab56-9d327c42d81f')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-a9a99afc-584a-4416-ab56-9d327c42d81f button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-a9a99afc-584a-4416-ab56-9d327c42d81f');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
  </div>


<br/>

<br/>

# 참고자료

---

- [App Review 수집](https://chaelist.github.io/docs/webscraping/app_review/)

