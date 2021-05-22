---
layout:     post
title:      Klaytn(클레이튼) Application 만들기 - 4
author:     bcnote3314
tags: 		blockchain Klaytn 클레이튼
subtitle:  블록체인 정복기	
category: Block
---


# 클레이튼으로 Bapp 만들기 - 스마트 계약!

Bapp이란 Blockchain Application의 준말이다. 
일반적으로 블록체인 환경에서 개발하는 Application을 Dapp이라고 칭하는 경우가 많다.
Dapp은 Decetralized applications의 준말로 탈중앙화된 어플리케이션을 칭한다.

하지만 완전한 탈중앙화는 현재 어려움이 많은 실정이다. 블록체인을 일부 활용하더라도 기존의 방식처럼 외부 서버도 같이 사용하는 형태로 개발이 충분히 가능하며 이는 기존의 Dapp과는 약간 다른 양상이다.
완전한 탈중앙화는 아니지만 블록체인을 활용하여 만든 Applcation이 Bapp이라고 생각하면 될것 같다.

이번 내용은 클레이튼으로 Bapp을 개발하기 위한 각종 툴에 대한 설명이다.

## 클레이튼 개발환경

- 이더리움의 비잔티움 버전에서 Fork (기존 이더리움 개발자들의 적응이 쉬움)
- 이더리움의 web3.js 처럼 클레이튼에도 carver.js가 존재함
- solidity 언어를 지원하며 truffle 프레임워크도 지원함

## 클레이튼 개발을 위하여 기본적으로 필요한 툴

- Wallet (계정 관리)
- Scope (트랜잭선 정보 조회)
- IDE (스마트 계약 코드를 작성하고 블록체인 네트워크에 접하여 테스트)

## Wallet (바오밥)

[바오밥](https://baobab.wallet.klaytn.com)

바오밥은 클레이튼의 퍼블릭 테스트넷이다. 
해당 경로에 접속해보면 wallet의 관리도 가능하며 scope 기능도 존재한다.

바오밥을 통해서 테스트를 위한 클레이를 받을수 있다.
또한 송금이나 기타 wallet에서 수행하는 기능들을 진행할수도 있다. 하지만 이것은 메인넷이 아닌 테스트 넷이기 때문에 실제 돈과는 상관이 없다.

### keystroe 파일

클레이튼의 wallet을 만들때 생성되는 파일이다. 해당 파일 생성시 비밀번호를 입력하게 되어있다.

이 파일과 비밀번호는 통장을 다른사람이 함부로 만지지 못하도록 금고에 넣어두겠다는 개념으로 사용된다.
여기서 통장이 비밀키가 되고 금고가 키스토어와 비밀번호 조합이다.

만약 비밀키가 알려지더라도 키스토어 파일과 비밀번호를 알지못한다면 사용을 할수가 없다.
키스토어가 노출이되더라도 비밀번호까지 공개되어야한다. (물론 그래도 파일 또는 비밀번호의 유출을 조심해야한다.)

### 송금, 스마트 계약, gas

클레이튼에서 단순한 송금에 필요한 gas와 gaslimit이 정해져있다. (고정이라 변하지 않는다.)
하지만 스마트 계약을 이용한다면 트랜잭션의 복잡성에 따라 gaslimit이 바뀌기 때문에 트랜잭션 비용이 달라질수 있다.

gas에서 사용되는 금액의 단위는 stone이다. 클레이튼의 코인의 단위는 공식문서에 제공되어있으며 자동으로 비율을 변환해주는 사이트가 있으니 나중에 참고하면 좋을듯 하다.

[공식 문서](https://docs.klaytn.com)
[비율 변환 및 기타 도움](https://blockchains.tools/pebConverter?l=KLAY)


## 클레이튼 IDE

[클레이튼 IDE](http://ide.klaytn.com/)

이더리움의 Remix IDE와 비슷하게 Web환경에서 solidity를 통하여 스마트 계약을 구현할수 있다.
다만 차이점이라고 하면 이더리움이 아닌 클레이튼을 통한 테스트 환경을 제공한다는 점이다.

> solidity의 문법에 대한 내용은 추후 별도의 Post로 정리하며 현재 Post에서는 툴의 기본 설명 정도만 정리한다.

![IDE1](http://drive.google.com/uc?export=view&id=1se74gZLRgs8m21PuGpzFQZvATiW7OQ2U)

클레이튼에서는 기본적으로 solidity 0.4.24 version을 사용하고 있으며 기본 네트워크 환경으로는 바오밥을 지원한다.

![IDE2](http://drive.google.com/uc?export=view&id=1Wlor-5zRaXT30xfvVf5eOG0RH2R3TYV_)

강의에서는 따로 설명되어 있지 않지만 현재 클레이튼이 메인넷이 런칭된 상태이기 때문에 메인넷(cypress)도 연결 가능하다.

![IDE3](http://drive.google.com/uc?export=view&id=19AZyHQHcg_6hk1i_dR7OpxAtTRmB9rhD)

또한 만약 개인적으로 운영하거나 믿을만한 endpoint node를 알고 있다면 해당 URL 주소를 통하여 접근하는 방식도 가능하다.

![IDE4](http://drive.google.com/uc?export=view&id=1b53aOE3PshdpbXW8XeUoDS2OkXq7Frgk)

private key 혹은 keystore + password 조합을 통하여 계정정보를 입력하면 현재 계정에 보유중인 Klay가 나온다.

강의에서는 Dev Node 라는 환경이 존재하여 개발자 테스트용으로 100Klay가 들어있는 계정을 연결해주는것이 있었는데 현재 접속해보면 이런 환경은 지원하지 않는 것으로 보인다. (못찾은것일수도 있다...)

![IDE5](http://drive.google.com/uc?export=view&id=1ir0GC2cmvUr5EPXAVY48EMZosCTNAYLQ)

테스트용 스마트 계약을 작성하고 deploy를 해보면 트랜잭션 정보가 화면에 출력된다.

![IDE6](http://drive.google.com/uc?export=view&id=15_oxl-H618G2rqUhCmxEWF84JZMxZORD)

어떤 주소에 배포가 되어있는지 알수 있으며 또한 작성한 스마트 계약에 대한 결과도 확인이 가능하다.
owner라고 하는 변수에 생성자를 호출한 사람의 주소를 저장하도록 코드를 작성했는데 생성자를 호출한 사람은 결국 스마트 계약을 배포한사람이 되고 주소를 비교해보면 내 로그인 정보와 일치하는 것을 확인할수 있다.
또한 자세히보면 트랜잭션이 발생함에 따라 가스비용 처리 또한 된것을 확인할수 있다.

![BOB](http://drive.google.com/uc?export=view&id=14NiGxzl1uxVSa56LY2Yb3re_EtL6OpWG)

테스트 이후에 바오밥에 해당 계정으로 접속해보면 트랜잭션 정보가 갱신된 것을 볼수 있다. 
