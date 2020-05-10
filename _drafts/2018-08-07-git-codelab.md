---
layout: post
title: 깃허브 협업 프로세스 연습하기 (Part-1)💑
comments: true
category: [github]
tag: [develop, co-work]
excerpt_separator: <!-- more -->
---
<img alt="옥토캣" src="/images/post_img/20180806-0.png" width="140" align="left" style="padding: 0px 10px 0px 0px;">

✨__GitHub에서 협업__ ✨하는 과정을 아톰 에디터에서 연습해 봅니다.. 항상 혼자 만드는 것이 익숙한 입장에서 처음에는 적응하는 과정이 필요하기 때문에, 그리고 제일 궁금했던 것은 과연 **'Merge'** 과정에서 업데이트 관리와 처음 접하는 사람을 힘들게 만드는 것, **Conflict** 가 발생 하는 과정을 확인해 보기 위함 입니다.
<!-- more -->

> * 개인으로 공유된 된 깃스터브 교육자료를 기반으로 연습 하는 과정입니다.
> * 원본 소스보기 : [[owo-sbsoft : git codelab]](https://github.com/owo-sbsoft/lotto)


<br>

| <img src="/images/post_img/20180806-000.png" width="400"> |
|:-----------------------------------------:|
| **Fig.01** - 개인공유 된 발표자료를 기반으로 연습. |


<br><br>

| <img src="/images/post_img/20180806-0000.png" width="550"> |
|:-----------------------------------------:|
| **Fig.02** - 아름다운 커플 협업 챠트 *.*;; |

* 예시로 제시 된 아름다운 지시와 협업.. [[커밋 참조]](https://github.com/owo-sbsoft/lotto/commits/master)
* <strike>언제 쯤 아름다운 협업을 해 볼 수 있을까.. </strike>

<br><br>
# 1.0 자주 쓰는 Branch (요약)

<img src="/images/post_img/20180806-002.png" width="550">

* **'develop'** : 개발중인 프로그램의 소스를 총 관리하는 브랜치
* **'feature'** : 프로그램의 기능 단위로 나누어 개발하는 브랜치
* **'release'** : 출시 직전의 소스를 관리하는 브랜치 (베타 버전)
* **'hotfix'** : 출시 이후, 발생한 버그를 긴급수정 Master에 바로 반영해야 하는 경우
* **'master'** : 상용화 되는(혹은 출시하는) 버전의 소스를 관리하는 브랜치


<br><br>
## 1.1 O.K! 연습하자!

* 원본의 기능을 개선해 보자 .. 디자인 개선
* 단색 보다는, 진짜 로또공 같은 <strike>생생한</strike> 컬러를 입혀보자

| <img src="/images/post_img/20180806-006.png" width="550"> |
|:-----------------------------------------:|
| **Fig.03** - 뭘 좋아 할 지 몰라서 48개를 준비했어! |

> * 한 땀, 한 땀 수작업으로 Color pick 을 해서 무지개색 파렛트를 준비
> * 컬러감각이 처음 치고는 나쁘지 않은 듯 (~*뿌듯*~)
> * 48개의 파렛트에서 랜덤으로 컬러를 뽑아 쓰기로 했다
> * 루프안에서 48개의 색상을 무작위로 선택해 주자



```java
var html_lotto = "";

for(var i=0; i<lottoNumbers.length; i++) {

  // 로또컬러스 리스트에서 랜덤 피킹
  colorPick = Math.round(Math.random()*48);
  html_lotto += '<b style="background: '+ lottoColors[colorPick] +'">'+ lottoNumbers[i] +'</b> ';
  }

document.getElementById("NUMBERS").innerHTML = html_lotto;
```
- 리스트에서 뽑아쓰는 코드를 추가하고



<img src="/images/post_img/20180806-003.png" width="550">

- 열심히 **'develop'** --> **'release'** --> **'master'** 로 배포를 했더니..
- 사소 한 실수 발견, 0 부터 카운트인데.. 48 -> 47 로 수정

> * 마스터에서 긴급수정이 발생해서, `'git checkout -b hotfix'` 로 분기 시킴
> * `'hotfix'` 브랜치에서 긴급 에러를 수정하고, **'Master'** 와 **'Develop'** 에서 각각 **'Merge'** 시킨다
> * 여기까지 **'git push'** 를 하면 아래와 같이 **'Network'** '챠트'가 완성된다.
<<<<<<< HEAD
> * **'Master'** 와 **'Develop'** 에서 각각 **'Merge'** 를 했기 떄문에 3개의 브랜치(마스터, 디벨롭, 핫픽스)가 나란히 같은 위치에 자리를 잡았습니다.

| <img src="/images/post_img/20180806-001.png" width="550"> |
|:-----------------------------------------:|
| **Fig.04** - 세 **브랜치** 가 나란히 정렬! |



> * 새로운 포스트(draft)를 추가 하기 위해서 **'Develop'** commit 을 1단계 진행 하고, push 합니다.
> * 그러면, **'Master'** 와 **'HotFix'** 를 뒤로하고, **'Develop'** 브랜치가 한단계 앞으로 치고 나가게 됩니다.

| <img src="/images/post_img/20180806-004.png" width="550"> |
|:---------------------------------------------------------:|
| **Fig.05** - 이제 **'Develop'*** 브랜치가 한 칸 앞으로~!! |



<br><br>
# PART.02 ... To be continued ...

- 뭔가 꼬불 꼬불 꼬여 버린 듯 한 네트워트
- HotFix 에서 뭔가 잘 못 되어버린 듯.. **Stash & Clear** 로 강제 조정 했더니..

| <img src="/images/post_img/20180806-005.png" width="550"> |
|:---------------------------------------------------------:|
| **Fig.06** - 뭔가 꼬이는 것 같은 것은 기분 탓??!! |

- 해보니, 종종 **'git push'** 뒤에 **'--all'** 을 잊는다
- '핫픽스(HotFix)' 후 '배포(release)'와 '개발(develop)'에 보내는 것도 종종 잊는다.
- 이것도 손에 익으려면, 얼마간의 적응기간이 필요 할 듯... <strike> 어차피 온자 하지만.. </strike>

~~ __끝__~~
