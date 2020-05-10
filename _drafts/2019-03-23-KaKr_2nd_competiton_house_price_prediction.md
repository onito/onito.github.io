---
layout: post
title: -Kakr-2nd- 파이썬 Pandas를 활용한 '탐색적 데이터 분석'(EDA)🐸
comments : true
category: [pandas, Kaggle]
tag: kaggle
excerpt_separator: <!-- more -->
---
<img alt="판다스아이콘" width="130" align="left" style="padding: 0px 10px 0px 10px;" src="/images/post_img/20190323-00icon.png" >

통계 및 데이터 분석 계의 깃허브 **Kaggle.com** 에 올라온 이벤트대회에 도전하기 위하여 국내의 분석커뮤니티, 케글코리아(KaKr)가 정기적으로 커뮤니티 이벤트를 주최한다. 문제는 캐글에 포스팅 된 문제를 기반으로 동호회 자체적인 랭킹을 평가하기 위해 한국 내수용(?) 분기 이벤트 대회 2번째 입니다.
<!-- more -->

# A Simple Regression Analysis (OLS model)   
* **Ordinary Least Squares model** using Python Pandas

 1. original **kaggle** Kernel site = [**House Sales in King Country, USA - Predict house price using regression**](https://www.kaggle.com/harlfoxem/housesalesprediction)

  1. **KaKr** competition site = [**https://www.kaggle.com/c/2019-2nd-ml-month-with-kakr**](https://www.kaggle.com/c/2019-2nd-ml-month-with-kakr)

 1. **EDA** (Exproratory Data Analysis) reference site = [**House Price Prediction EDA with R**](https://www.kaggle.com/psystat/house-price-prediction-eda-with-r)

  1. **OLS** regression model reference site = [**Data Science School - Korean**](https://goo.gl/LUmjbD)

![헤더]({{site.baseurl}}/images/post_img/20190323-01header-house-price.png)


```python
import os

import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt

import statsmodels.api as sm
from sklearn.model_selection import train_test_split
from sklearn import linear_model as lm
```

## Analysis Introduction   

> **Data Cleaning and Visualisation**     
>
> - This section will revolve around exploring the data and visualising some summary statistics.

> **Stability Selection via Randomised Lasso Method**     
>
> - Introduce a relatively new feature selection method called "Stability Selection" and using the Randomised Lasso in its implementation

> **Recursive Feature Elimination**     
>
> - Implementing the Recursive Feature Elimination method of feature ranking via the use of basic Linear Regression

> **Linear Model Feature Coefficients**     
>
> - Implementing 3 of Sklearn's linear models (Linear Regression, Lasso and Ridge) and using the inbuilt estimated coefficients for our feature selection

> **Random Forest Feature Selection**     
>
> - Using the Random Forest's convenient attribute "feature_importances" to calculate and ultimately rank the feature importance.

## Dataset (X_concat) classified by id

| **ITEM** | Start **id**) | End **id** | **data** Quant' | **shape** | **Variables** | **Remark** |
|---------------|--|--|--|--|--|--|
| **train data-set** | 0 | 15,034 | 15,035 개| (15035,19) | **X**|  |
| **label data-set** | 0 | 15,034 | 15,035 개| (15035, 1) | **y**|  |
| **test data-set** | 15,035 | 21,502 | 6,468 개| (6468, 19) | **X_pred**|  |
| ~~**label data-set**~~ | **N/A** | **N/A** | 0 개| **N/A** | ~~**y_pred**~~ | ~~~**Hidden (!)**~~~  |
| - | - | - | - | - | - | - |
| **cross train-data set** | 0 | 10,524 | 10,525 개| (10525,19) | **cross_X**| **70%**  |
| **cross label data-set** | 0 | 10,524 | 10,525 개| (10525,1) | **cross_y**| **70%**  |
| **cross pred train data-set** | 10,525 | 15,034 | 4,510 개| (4510,19)| **cross_X_pred**| **30%** |
| **cross pred label data-set** | 10,525 | 15,034 | 4,510 개| (4510,1) |**cross_y_pred**| **30%** |
| - | - | - | - | - | - | - |
| **concatenated data-set** | 0 | 21,503 | 21,504 개| |**X_concat**| **train + test**  |



```python
# check path ... 폴더만 보는 옵션 /ad

!dir /ad
```

## X(2d), y(1d) - The Original Train Data-set


{% highlight python linenos %}

# Data의 위치 .. 저장장소에 따라 달라질 수 있음.
dir_data = './baseline_submission/data/'

X = pd.read_csv(dir_data + 'train.csv', index_col='id')
y = pd.DataFrame(
        data=X['price'],    
        index=X.index,
    )

# del X['price']                         # 동일한 명령인데, 멋지게 X.drop()으로 지우자!
X.drop(columns='price', inplace=True)  # inplace 옵션이 있어야 지워진다. (주의!)
X.columns
# Index(['date', 'bedrooms', 'bathrooms', 'sqft_living', 'sqft_lot', 'floors',
#        'waterfront', 'view', 'condition', 'grade', 'sqft_above',
#        'sqft_basement', 'yr_built', 'yr_renovated', 'zipcode', 'lat', 'long',
#        'sqft_living15', 'sqft_lot15'],
#       dtype='object')

X.shape    # (15035, 19)
y.shape    # (15035, 1)

X_pred = pd.read_csv(dir_data + 'test.csv', index_col='id')
X_pred.shape    # (6468, 19)
# y_pred ... N/A, Unknown - (Skipped)

{% endhighlight %}


{% highlight python linenos %}
# Data Cleanness Check-up
X.isna().sum()             # none
X.isnull().sum()           # none

X_pred.isna().sum()        # none
X_pred.isnull().sum()      # none

X.info()
{% endhighlight %}

## ConCatenate 2 Data-sets (X and X_pred)


{% highlight python linenos %}
# 두개의 데이터를 Concatenated 시킨다 (연결)

X_concat = pd.concat((X, X_pred), axis=0)
X_concat.shape    # (21503, 19)
{% endhighlight %}

## Cross Validation Data-set (cross_X, cross_y)
to preventing over-fitting, split the train data-set into train and test set for cross validation set, it will be divided into 70:30 proportion within the original train data-set
- X(2d), y(1d) - = The original data-set
- cross_X, cross_y = Train Data-set (proportion of 70% from X,y)
- cross_X_pred, cross_y_pred = Test Data-set (proportion of 30% from X,y)

through this procedure, we can check wheather the prediction model is over-fitted or not, because we don't know the exact answer of the original test data-set, we can't check the established model's over-fitting on the original test data-set



{% highlight python linenos %}
# X_concat 에서 분리하는 게 아님 (주의)
# 학습데이터 (70%) / 교차검증 (30%) 데이터를 분리한다.
# X 에서 70% : 30% 를 분리해 낸다.

cross_X = X.loc[:10524,]      # (10525, 19)     ... 70%
cross_y = y.loc[:10524,]      # (10525, 1)      ... 70%

cross_X_pred = X.loc[10525: ,]     # (4510, 19) ... 30%
cross_y_pred = y.loc[10525: ,]     # (4510, 1)  ... 30%

cross_X.shape              # (10525, 19)
cross_X_pred.shape         # (4510, 19)

cross_y.shape              # (10525, 1)
cross_y_pred.shape         # (4510, 1)

from datetime import datetime

# datetime.strptime()을 사용해서 파싱한다 --> '%Y%m%dT000000'
# X_concat['date'] = [datetime.strptime(date_str,'%Y%m%dT000000') for date_str in X_concat['date']]
X_concat['date'] = X_concat['date'].map(lambda x: datetime.strptime(x,'%Y%m%dT000000'))

plt.figure(figsize=(10,4))
plt.hist(X_concat['date'], bins=200)
plt.title('number of the transaction event')
plt.show()
{% endhighlight %}

![헤더]({{site.baseurl}}/images/post_img/20190323-02hist-transaction-events.png)



## Quick-Overviewing of The Transaction Events,

1. ** The Seasonal Tendancy **
    > - December to March : Low-Transaction Period ( Easy-Guess, common)
    > - June to August : Growing Transaction, but Just single annual data-set is not enough
1. **The Weekly Tendancy**
    > - Sparse days are seen, check! --> Sat. Sun. (Weekend) making sense
    > - Also has weekday tendancy, but skipped this time.

## Check-up for The Weekly Changes
1. Transactions are drastically reduced on Weekend(Sat & Sunday) but stil exist.
1. Why? Real estate dealers won't work on weekend?
  - [Calendar for Year 2014](https://www.timeanddate.com/calendar/?year=2014&country=27) (**United States**)
  - Check!! --> 15th June, 2014 is Sunday (the guess was right!)
  - Because **Buyers** want to have a relaxing weekend as well as **Dealers** ?



![헤더]({{site.baseurl}}/images/post_img/20190323-03hist-transaction-price-season.png)


{% highlight python linenos %}
## 주간 주택거래량  변동확인
# 1. 거래량의 주간단위 변화를 보면, 토,일에 거래가 대폭 줄어드는 것은
# 1. 주말에는 부동산 중개사가 일을 안 하기 때문인가?
#   - https://www.timeanddate.com/calendar/?year=2014&country=27
#   - 확인 --> 2014년 6월 15일 미국동부 시간 = 일요일이 맞다.
#   - 거래자가 주말에는 쉬고 싶어서 인가? / 중계사가 쉬고 싶어서 인가?

pd_temp2014 = X_concat.loc[
        (X_concat['date'] >= pd.to_datetime('2014-05-01')) &
        (X_concat['date'] <= pd.to_datetime('2014-12-31'))
    ]

pd_temp2015 = X_concat.loc[
        (X_concat['date'] >= pd.to_datetime('2015-01-01')) &
        (X_concat['date'] <= pd.to_datetime('2015-05-31'))
    ]

plt.figure(figsize=(10,10))

plt.subplot(411)
plt.hist(X_concat['date'], bins=(7*100))

plt.subplot(412)
plt.bar(X['date'],y['price'], alpha=0.5)

plt.subplot(413)
plt.hist(pd_temp2014['date'], bins=(7*100))

plt.subplot(414)
plt.hist(pd_temp2015['date'], bins=(7*100))

plt.show()

{% endhighlight %}
