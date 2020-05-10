---
layout: post
title: 비트코인 마이닝=작업증명(POW:Proof of Work)란 무엇인가?👷
comments: true
category: [block_chain]
tag: [POW, nonce]
excerpt_separator: <!-- more -->
---

<img alt="작업증명!" width="125" align="left" style="padding: 0px 10px 0px 0px;" src="/images/post_img/20181012-00.png" >

암호화폐의 가장 핵심 아이디어인 해쉬함수에 대해서 알고나면, 그 다음은 마이닝(mining) 과정에서 필수적인 작업증명(POW : proof of work)에 대해서 알아야 한다. 채굴자(miner)가 블럭을 발굴하기 위해서 이만큼 고생을 했다는 증명이다. 이 작업증명(POW)의 과정에서 논스(nonce)와 난이도(difficulty)가 나온다. <!-- more -->

난이도(difficulty)는 블럭을 채굴하기 어렵게 하는 변수인 것은 쉽게 받아 들이겠는데, 논스(nonce)라는 개념은 잡기가 어렵다.



<br><br><br>
# 100 이하 숫자의 랜덤 샘플링

100개의 번호가 매겨진 공을 안 보이는 통에 넣고, A, B, C 세 사람이 나와 각자 공을 뽑는다, 조건에 만족하는 숫자가 나오면 통과가 되지만, 그렇지 않으면 다시 공을 집어넣고 뽑기를 반복한다. 여기에 확률적인 난이도를 높이기 위해 조건이 붙는다.
> <img src="/images/post_img/20181012-01pick_the_ball.png" width="450">
>
> -   A 는 `90` 보다 작은 수를 뽑으면 통과 (작업증명)을 받아준다.  
> -   B 는 `50` 보다 작은 수를 뽑아야 통과 (작업증명)을 받아준다.    
> -   C 는 `5` 이하의 숫자를 뽑았을 때만 통과시켜 준다.


누가 빠른시간 내에 조건을 충족하고 작업을 마칠 수 있을까? 당연히 A가 월등히 적은 횟수로 작업을 마칠 수 있다

예를 들어,

> 1.  A 는 5번 만에 90보다 작은 수를 뽑아 통과했다.    
> 2.  B 는 13번 만에 50보다 작은 수를 뽑아 통과했다.    
> 3.  C 는 59번 만에 5보다 작은 수를 뽑아 통과했다.

항상 C 가 더 만은 횟수를 시도해야 되는 것은 아니다. 한번에 통과할 수도 있다.  
하지만, 확률적으로 C 가 더 많이 고생해야 할 것이다.    



<br><br>
<img src="/images/post_img/20181012-03pick_random.png" width="650">



<br><br>
여기서,    

1.  `90 보다 작은 수` 라는 `조건`은 **난이도** (`difficulty`)
2.  **반복작업** `5번` 의 횟수가 **논스**(`nonce`) 다

**nonce** 는 몇 번을 반복해서 시도 했는지, 횟수이며, 이 횟수가 얼만큼 고생했는지를 입증하는 작업증명 (proof_of_work)이다. 논스를 포함해서, 현재의 Hash 값이 계산되야 하며, 유일하게 변경할 수 있는 해쉬입력값 통로 NONCE 변경 시켜 가면서 해쉬값을 반복적으로 계산하다 보면 **우연하게** 조건을 만족하는 해쉬값을 찾아낸다. 빠르게 계산할수 있는 능력을 가진사람에게 기회가 더 많지만, 찾을 확률은 **우연** 에 기반하기 때문에 절대적인 것은 아니다 <strike>..이거, 다 새빨간 거짓말인거 다 아시죠? 여러분!..</strike>



<br><br>
# 아래 블럭결과를 비교해 보자

난이도에 따라서, 기준보다 작은 해쉬를 계산했을 때 시도한 횟수, **논스(nonce)** 를 보여준다, 일정크기 이하의 해쉬를 뽑아내기가 어려운 만큼 시도횟수 논스가 증가 했다.

<br><br>

<pre>
{'difficulty': '000',
 'hash_present': '000107d0d540f0edf7eb0af657840b51',
 'hash_previous': '000005fa8482b821aff9b2ce6103f69e',
 'index': 1,
 'nonce': 7725,
 'timestamp': 1539440895.4122593,
 'transaction': [{'amount': 1000, 'recipient': 'Bob', 'sender': 'Alice'},
                 {'amount': 800, 'recipient': 'Alice', 'sender': 'Scrouge'},
                 {'amount': 200,
                  'recipient': 'node_identifier_uid',
                  'sender': 'coinbase_reward'}]}
</pre>

-   **시도횟수(논스) = 7,725회**
-   만족하는 present_hash 값을 찾았을 때,
-   타임스탬프를 찍고, 현재해쉬값을 추가했다
-   즉, 타임스탬프와 현재해쉬값을 뺀 블럭을 해쉬256 함수에, 논스값과 같이 넣으면
-   현재의 조건에 만족하는 해쉬값이 나온다.
-   해쉬 입력값에서 논스를 포함한 그 어떤글자가 한글자만 바뀌어도,
-   해쉬값은 조건에서 벗어난다.
-   즉, 특정 논스값을 찾아야만, 조건에 맞는 해쉬값 작업증명을 이뤄낸다.
-   해쉬값은 무작위수이기 때문에, 논스를 1씩 증가시키며, 반복 계산한다
-   이 과정이 랜덤피킹과 같은 동작이다.

<br><br>

<pre>
{'difficulty': '00000',
 'hash_present': '00000da730623b47a1e4d39a4d28ccfe',
 'hash_previous': '000005fa8482b821aff9b2ce6103f69e',
 'index': 12,
 'nonce': 52305,
 'timestamp': 1539441010.7054014,
 'transaction': [{'amount': 1000, 'recipient': 'Bob', 'sender': 'Alice'},
                 {'amount': 800, 'recipient': 'Alice', 'sender': 'Scrouge'},
                 {'amount': 200,
                  'recipient': 'node_identifier_uid',
                  'sender': 'coinbase_reward'}]}
</pre>

-   난이도가 두자릿수 추가로 떨어지니가, **시도횟수가 52,305회** 로 늘어났다.
-   조건에 맞는 해쉬값을 계산할수 있는 입력갑 논스를 찾아내고
-   조건에 맞는 해쉬값을 찾아내면 작업증명이 이루어진다.
-   각 블럭의 연결고리는 조건에 맞는 해쉬값을 기준으로 앞뒤로 연결되어있다.
-   조건에 맞는 해쉬값를 찾아서 앞/뒤 블럭을 잠근것이다.
-   길이가 긴, 일련의 블록들은 앞/뒤 무결성을 해쉬값으로 증명한다.
-   늘어선 블럭의 내용이 하나라도 바뀐다면, 뒤에 블럭은 모두 무효가 된다.
-   해킹을 위해 중간값을 바꾸려면, 그 이후 블럭의 작업증명을 모두 바꿔야 한다.
-   이 과정에서 작업증명을 하는데, 너무 많은 시간이 소요된다.

<br><br>

<pre>
{'difficulty': '0000000',
 'hash_present': '0000000f8fc7d21dc1a4c67f3ec72ac5',
 'hash_previous': '000005fa8482b821aff9b2ce6103f69e',
 'index': 12,
 'nonce': 19760524,
 'timestamp': 1539441177.5858967,
 'transaction': [{'amount': 1000, 'recipient': 'Bob', 'sender': 'Alice'},
                 {'amount': 800, 'recipient': 'Alice', 'sender': 'Scrouge'},
                 {'amount': 200,
                  'recipient': 'node_identifier_uid',
                  'sender': 'coinbase_reward'}]}
</pre>

-   난이도가 네자리 추가로 떨어져서, **시도횟수가 19,760,524번** 으로 늘어났다.
-   블럭이 뒤로 갈수록 난이도가 증가하며, 작업증명에 시간이 기하급수적으로 늘어남.
-   중간블럭 내용을 위조하면, 이후 블럭의 모든 작업증명을 바꿔야 해킹이 완성되는데,
-   블럭은 10분마다 추가로 생성된다.
-   게다가, 일련의 블럭모음 정보는 모든 노드들이 자동으로 공유하고 있다.
-   따라서, 노드간에 공유하고 있는 모든 블럭을 일순간에 해킹 하는것은 불가능 하다.

<br><br>

| <img src="/images/post_img/20181012-02chains.png" width="450"> |
| :----------------------------------------------------------: |
| [image ref.]: [Understanding How Blockchain Works](https://blog.ndcconferences.com/understanding-blockchain/) |

- 블럭해쉬의 입력 값을 변경시킬 수 있는 유일한 변수는 논스(nonce)다.
- 논스를 변경하며, 해쉬값을 계산해서 난이도조건을 만족하는 값이 나올 때 반복
- 논스는 0 에서부터 +1씩 증가시키며 반복 계산하기 때문에 결과적으로 계산 횟수다.


<br><br>
# 논스(nonce)에 대한 예제 설명영상

-   논스(nonce)와 작업증명(POW)에 대해서 아주 잘 설명 해 놓은 영상

<iframe width="560" height="315" src="https://www.youtube.com/embed/_160oMzblY8" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>





<br><br>
# CODE REVIEW

난이도를 바꿔가면서, 논스를 계산 해 보자.  
코드를 정제하는데, 별로 시간과 노력을 들이지 않았으니, 과정만 참조하세요~ 😄  
깔끔하게 `Refactor` 해서, **Pull Request** 를 주셔도 좋을 것 같습니다만,

```python
"""
* nonce 만이 유일하게 변경할 수 있는 값이다. 값을 1씩 증가 시키며 찾는다.
* 우연히 해쉬값의 첫자리가 '0000'으로 시작하는 해쉬를 발견 했을 때,
* 작업 증명을 완성 한다. 작업증명(Proof of Work) 값은 nonce=7725 이다.
"""
import time

from pprint import pprint
from hashlib import sha256

mining_uid = 'node_identifier_uid'
transactions = [
    {
        'sender': 'Alice',
        'recipient': 'Bob',
        'amount': 1000
    },
    {
        'sender': 'Scrouge',
        'recipient': 'Alice',
        'amount': 800
    },
    {
        'sender': 'coinbase_reward',
        'recipient': mining_uid,
        'amount': 200
    },
]


last_block = {
    'index': 12,
    'difficulty': '0000',
    'nonce': 0,
    'hash_previous': '000005fa8482b821aff9b2ce6103f69e',
    'transaction': transactions,
}


def get_hash_w_nonce(last_block, nonce):
    """ 최근블럭에 논스를 대입하여 해쉬값을 리턴한다"""
    last_block['nonce'] = nonce
    hash = sha256(str(last_block).encode()).hexdigest()
    return hash


def add_header(last_block, block_hash):
    """ 블럭에 타임스탬프와 현재해쉬를 추가한다"""
    header = {
        'timestamp': time.time(),
        'hash_present': block_hash, }

    for _key, _val in header.items():
        last_block[_key] = _val

    return last_block


def proof_of_work(last_block):
    nonce = 0
    difficulty = last_block['difficulty']

    while True:
        hash = get_hash_w_nonce(last_block, nonce)

        if hash[:len(difficulty)] == difficulty:
            hash_present = hash[:32]
            add_header(last_block, hash_present)
            return last_block

        nonce += 1


if __name__ == '__main__':
    last_block = proof_of_work(last_block)
    pprint(last_block)
```
