---
layout: post
title: 주피터 노트북에 Word Cloud 만들기🍐
comments : true
---
✨**주피터 노트북** ✨ (Jupyter Notebook) 을 이용해서, 문장에서 출현빈도를 글씨크기로 표현하는 워드클라우드(Word Cloud)를 만들어 보자, 워드 클라우드는 소셜네트워크 분석에 자주 사용 되는 시각화 기법이다.

## 주피터 노트북을 이용해서, 워드 클라우드를 만들어보자

텍스트에서 어떤 단어들이 자주 출현하는지 그 빈도에 세어서 글씨크기로 표현해 주는 워드클라우드는 요즘 ✨페이스북✨, 트위터등 ✨**소셜네트워크 분석** ✨에 자주사용되는 기법입니다.

![__지킬로고__](https://jekyllrb-ko.github.io/img/logo-2x.png)

>그 워드클라우드를 연습해 보기위해서, 마틴 루터킹 목사의 연설문 'I Have A Dream'을 분석해서 워드클라우드를 만들어 보고, 클라우드 그림배경에 넣어보기 위해서, '이상한 나라의 엘리스'의 엘리스 실루엣 마스킹(검은배경)에 워드 클라우드를 넣어보겠습니다.

> 여기서 사용하는 방법은 MD (Mark Down)라고 하는데, ✨HTML(Hyper Text Mark-Up Language)✨의 마크업과 대치되는 이름(마크다운)입니다. 잠시 마크다운 사용법에 대해서 알아보면❓ ,

## MD ['마크다운'](https://namu.wiki/w/%EB%A7%88%ED%81%AC%EB%8B%A4%EC%9A%B4) 이란? (나무위키)🌲

마크다운 (Markdown)은 마크업 언어의 일종으로, 존 그루버(John Gruber)와 아론 스워츠(Aaron Swartz)가 만들었다. 읽기도 쓰기도 쉽다는 장점이 있다. 그루버는 마크다운으로 작성한 문서를 HTML로 변환하는 펄 스크립트도 만들었다. 확장자는 .md를 쓴다.

HTML과 가장 큰 차이점은 표준화가 안되있다는 점이 가장 큰 불편한 점입니다. HTML은 표준화그룹을 가지고, 꾸준히 가다듬어 온 반면, 마크다운은 거의 황야에 방치해 놓은 수준으로, 여기저기 다른 표준이 생겨났습니다.

![__pic00__]({{ site.baseurl }}/images/post_img/2018-05-28_0_github.png)

> 마크다운의 가장 큰 백그라운드의 하나는 '깃허브'입니다. 그래서 가장 큰 표준 중에 하나가 바로 깃허브 표준 입니다. 하지만, 많이 불편합니다.

## 워드 클라우드 시작하기🍏

- word ✨ cloud modue with matplotlib, numpy image processing ✨
- Classified word by frequency of appearance

![__pic01__]({{ site.baseurl }}/images/post_img/2018-05-28-5_0.png)
![__pic02__]({{ site.baseurl }}/images/post_img/2018-05-28-5_1.png)


```python
%matplotlib inline
import os
import matplotlib.pyplot as plt
from wordcloud import WordCloud

DESTIN_DIR = '/home/pi/jupyter_notebook/_static/_temp/'
FILE_NAME = 'i_have_a_dream.pdb'

text = open(DESTIN_DIR + FILE_NAME).read()
wc = WordCloud(max_font_size=35).generate(text)
# If you lower the maximum font size, you will get more words

wc.words_
```
    {'Alabama': 0.15789473684210525,
     'Alleghenies': 0.05263157894736842,
     'America': 0.2631578947368421,
     'American': 0.21052631578947367,
     'Catholics': 0.05263157894736842,
     'Colorado': 0.05263157894736842,
     'Constitution': 0.05263157894736842,
     'Continue': 0.10526315789473684,
     'Free last': 0.15789473684210525,
     'Georgia': 0.15789473684210525,
     'God': 0.21052631578947367,
     'Let': 0.15789473684210525,
     'Let freedom': 0.5263157894736842,
     'Life': 0.10526315789473684,
     ...
     'will': 1.0,
     'will able': 0.42105263157894735,
     'words': 0.15789473684210525,
     'work': 0.10526315789473684,
     'wrote': 0.05263157894736842,
     'years later': 0.21052631578947367}

## 1. If you lower the maximum font size, you will get more words

> - wc = WordCloud(max_font_size=30).generate(text) .... Line.10

```python
plt.figure(figsize=(12,12))
plt.imshow(wc, interpolation='bilinear')
plt.axis('off')
plt.show()
```

![png]({{ site.baseurl }}/images/post_img/2018-05-28-3_0.png)

## 2. Masked Image Cloud

> - Word cloud putting on a Alice masked image

```python
%matplotlib inline

from os import path
from PIL import Image
import numpy as np
import matplotlib.pyplot as plt
from wordcloud import WordCloud, STOPWORDS

""" MASKED WORD CLOUD
Original Author's Page refer to HERE :
https://amueller.github.io/word_cloud/auto_examples/masked.html
"""
# d = path.dirname(__file__)
# # Read the whole text.
# text = open(path.join(d, 'alice.txt')).read()

DESTIN_DIR = '/home/pi/jupyter_notebook/_static/_temp/'
FILE_NAME = 'i_have_a_dream.pdb'
text = open(DESTIN_DIR + FILE_NAME).read()

# read the mask image taken from
# http://www.stencilry.org/stencils/movies/alice%20in%20wonderland/255fk.jpg
alice_mask = np.array(Image.open(DESTIN_DIR + "alice_mask.png"))

stopwords = set(STOPWORDS)
stopwords.add("said")

wc = WordCloud(background_color="white", max_words=2000, mask=alice_mask,
               stopwords=stopwords)

# generate word cloud
wc.generate(text)

# store to file
wc.to_file(DESTIN_DIR + "alice.png")

# show 1
plt.imshow(wc, interpolation='bilinear')
plt.axis("off")
plt.figure()

# show 2
plt.imshow(alice_mask, cmap=plt.cm.gray, interpolation='bilinear')
plt.axis("off")
plt.show()
```

![png]({{ site.baseurl }}/images/post_img/2018-05-28-5_0.png)

![png]({{ site.baseurl }}/images/post_img/2018-05-28-5_1.png)

## 3. Show Image Cloud Once Again Alone...
> - Repeatedly running the code

```python
plt.figure(figsize=(12,12))
plt.imshow(wc, interpolation='bilinear')
plt.axis('off')
plt.show()
```

![png]({{ site.baseurl }}/images/post_img/2018-05-28-7_0.png)

![__루비온레일즈__]({{ site.baseurl }}/images/system/ruby_on_rails.jpg)

### [[DOC] Original Refered from : http://pinkwink.kr/1029](http://pinkwink.kr/1029)
