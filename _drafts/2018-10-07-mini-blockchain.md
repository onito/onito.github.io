---
layout: post
title: 파이썬 미니블록체인 테스트(트랜잭션,작업증명,마이닝)🍪
comments: true
category: [block_chain]
tag: [cripto_currency]
excerpt_separator: <!-- more -->
---
<img alt="암호화폐!" width="125" align="left" style="padding: 0px 10px 0px 0px;" src="/images/post_img/20181007-00.png" >

파이썬을 이용하여, 간단히 블럭체인의 원리를 구현 해본다. 기본적으로 블록생성, 트랜젝션의 발생, 작업증명, 그리고 마이닝을 통해서 새로운 블록이 생성 되었을때, 그때 까지 모아진 트랜젝션 데이터를 모아서, 블럭의 거래정보에 기록하는 과정을 구현하고, Flask를 통해서 명령을 실행해 본다.
<!-- more -->

<br><br><br>
# BLOCKCHAIN for DUMMIES
> Everybody talks these days about the Blockchain. Everybody. But ask somebody about it. They will have a hard time trying to explain to you what is Blockchain.

| <img src="/images/post_img/20181007-01-intro.png" width="550"> |
|:----------------------------------------------:|
|[image ref.]: [Blockchain for dummies](https://goo.gl/nfdANj)|



<br><br>
# 0. INTRO 👋

I made some simple structure of blockchain, but this is not for the project, but for the own personal study, the better understanding how blockchain work in simple way.
So, please just refer it as one of many sample materials.



<br><br>

|　**Language**　|　**Library**　|　**Data form**　|　**Reference**　|
|:--------:|:--------:|:----:|:---------------------------------------------:|
|Python    |Flask     |JSON  |[https://goo.gl/M6XU5v](https://goo.gl/M6XU5v)|



<br><br>
# 1. HOW TO RUN

Run bash window on the target folder and type in 'python server.py'. Then the first block, index 1, will be automatically generated if there is no stored history file named **chain.json**.

regarding **chain.json** file, there's no chain.json at first. it is generated only if you like to. (I prepare 'write' menu on webpage)

When '**chain.json**', a written history file, exists, the block history will be restored autonomously. To avoid at first, I changed the file name to '**chain_old.json**', which has the several depth of block history.



<br><br>

| <img src="/images/post_img/20181007-01bash_run.png" width="550"> |
|:----------------------------------------------:|
|a |


<br><br>
# 2. HOW IT SHOWS

> 'http://localhost:5000' in browser



<br><br>

| <img src="/images/post_img/20181007-02chain_help.png" width="450"> |
|:----------------------------------------------:|
|a |



<br><br>
# 3. ABOUT JSON
In block_class, there're 3 json files.

> 1. **blcok.json** - the first intention, but to many hashs
> 2. **chains_old.json** - already described on the top.
> 3. **genesis.json** - Ethurium genesis block format, just refer to it

This is not exact as it can be, but just alludes coinbase transaction. Because it is compensation for mining, sender is nobody, recipient is randomly generated node ID and amount is set as 100, form of variable for mining reward.

<pre>
    {
     "sender": "0",
     "recipient": "17460cdf96bc403ca792bf08a7f87d4d",
     "amount": 100
    }
</pre>



<br><br>

| <img src="/images/post_img/20181007-03structures.png" width="250"> |
|:----------------------------------------------:|
|a  |


<br><br>
# 4. OTHERS
> As a beginner trying to bite a little dent on learning the blockchain technology, other constructive opinoins are always welcomed and let's make it together! 😏




<br><br>
# 5. REFERENCES
> 1. 블록체인 구현 (블록생성, 트랜잭션, 작업증명, 마이닝) -  https://goo.gl/M6XU5v
> 2. 파이썬으로 블록체인 개발 ( 아직 Part1밖에 없음) : https://goo.gl/V2owrp



<br><br>
# 6. CODE PART
> The rest of code part is at [**onitonitonito/block_chain_study**](https://github.com/onitonitonito/block_chain_study)


```python
"""
* Flask를 이용해서, 블록체인 API를 제공
"""
# import json
# from textwrap import dedent

from flask import Flask, jsonify, request, render_template, redirect
from uuid import uuid4

from block_class.block_chain import BlockChain


app = Flask(__name__)

# 32-bits 유니크 아이디를 생성한다 - '11864aaa-d1b2-45af-9c5a-c21dda71c6fc'
node_identifier = str(uuid4()).replace("-", "")

bc = BlockChain()


@app.route("/", methods=["GET"])
def index():
    return render_template('./chain_help.html')


@app.route("/chains", methods=["GET"])
def full_chain():
    response = {
        "chain": bc.chain,
        "length": len(bc.chain),
    }

    return jsonify(response), 200


@app.route("/mine", methods=["GET"])
def mine():
    # 마지막 블럭의 작업증명을 기준으로 POW을 계산한다 (보상100)
    last_block = bc.last_block
    last_proof = last_block["proof"]

    proof = bc.proof_of_work(last_proof)
    conpensation = 100

    bc.new_transaction(
        sender="0",
        recipient=node_identifier,
        amount=conpensation)

    previous_hash = bc.hash(last_block)
    block = bc.new_block(proof, previous_hash)

    response = {
        "message":         "... new block forged! ...",
        "index":           block["index"],
        "transactions":    block["transactions"],
        "proof":           block["proof"],
        "previous_hash":   block["previous_hash"]
    }

    return jsonify(response), 200


@app.route("/transactions/new", methods=["GET", "POST"])
def new_transaction():
    # 수신,송신,금액이 존재하면 거래를 기록하고 내용을 보여줌
    if request.method == "POST":
        # values = request.get_json()

        sender = request.form["sender"]
        recipient = request.form["recipient"]
        amount = request.form["amount"]

        values = {
            "sender": sender,
            "recipient": recipient,
            "amount": amount}

        required = ["sender", "recipient", "amount"]

        if not all(k in values for k in required):
            return "... missing values ...", 400

        index = bc.new_transaction(
            values["sender"],
            values["recipient"],
            values["amount"])

        response = {
            "message": "... Transaction will be added to Block {0}".format(index),
            "current TX": bc.current_transactions
        }
        return jsonify(response), 201

    else:
        return render_template("./new_TX.html"), 200


@app.route("/transactions", methods=["GET"])
def show_transaction():
    # 블록에 기록하기 위해 모인, 기록 전, 거래자료를 조회한다.
    response = bc.current_transactions
    return jsonify(response), 200


@app.route("/transactions/all", methods=["GET"])
def show_transaction_all():
    # 블록체인에서 모든 거래내역만 뽑아낸다 (채굴보상은 거래제외)
    echo = bc.show_all_transaction(bc.chain)
    echo = "<pre>" + echo + "</pre>"

    return echo

@app.route("/write", methods=["GET"])
def write_chains():
    # 이제까지 모인 체인블록을 Json 화일에 기록/보관한다.
    chains = bc.write_json()
    response = {
        "chains": chains,
        "message": "...  Writing whole chains to chains.json ..."
    }
    return jsonify(response), 200


if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000, debug=True)
```
