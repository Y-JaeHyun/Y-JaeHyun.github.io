---
layout:     post
title:      Klaytn(클레이튼) Application 만들기 - 2
author:     bcnote3314
tags: 		blockchain Klaytn 클레이튼
subtitle:  블록체인 정복기	
category: Block
---

# 클레이튼 이해하기!

클레이튼의 합의와 네트워크 구조 관련 내용 정리 
(public, private 체인등 아직 공부를 하지않은 상태이기 때문에 모르는 내용이 많다. 일단 정리하고 나중에 더 모자란 부분은 보충해야 겠다. *단어*는 나중에 주석으로 내용 채워야할 목록)

## 합의(Consensus)

블록체인의 종류에 따라 합의 알고리즘이 나뉘어 있다.

1. public 블록체인 : PoW(작업증명), *PoS*(자산증명) 등
1. private 블록체인 : *pBFT*, *Raft* 등

일반적으로는 private 블록체인이 더 효율적인 합의 과정을 도출한다.

*BFT*(비잔티움 결함 허용) 기반의 합의 과정이 보통 효과적이다.
참여노드 숫자를 제한하는 방식으로 합의 성능을 높인다. 하지만 노드의 제한은 분산화가 잘 이루어지지 않는다는 점과 투명성을 저하시킨다는 문제점을 발생시킬수 있다.

클레이튼의 합의는 IBFT(Istanbul Byzantine Falut Tolerance, 이스탄불 비잔티움 결함 허용) 방식을 사용한다.
IBFT는 강력한 보안성과 엔터프라이즈급 성능을 지향한다.


![IBFT](http://drive.google.com/uc?export=view&id=1YTkcClwiSqSIMoraX_V4JWKyQU8MbsJN)

우선 검증을하는 Validator들이 존재하는데 Propose 단계에서 한명을 Proposer로 결정한다. 선택 방식은 라운드 로빈 방식으로 돌아가면서 선택된다.
이후 합의를 위한 3단계를 수행한다.

1. Pre-prepare : Propser가 블록을 만들어서 다른 validator들에게 전달을 한다.
1. prepare : validator들이 자기가 받은것을 자신을 제외하고 다른 노드들에게 잘 받았다는 내용을 전달한다. (Propose 상태에서 Falut 판정을 받은 노드들은 응답이 없으며, 전체 노드들이 동일한 라운드의 동일한 순서상에 존재하는지 검증되는 단계이다.)
1. commit : 전달받은 블록을 수락할 것인지 소통을 하는 단계이다. 각자의 응답을 모두에게 전달하며 2/3 이상이 합의에 찬성하면 해당 블록은 바로! Final 상태가 된다. (뒤에 더 연결되고 뭐고 기다리는 시간이 없다.)

## 블록 생성 사이클(cycle), 제안 검증 전파 과정

클라이튼은 블록 생성 주기를 라운드(Round)라고 표현하며 한 라운드가 끝나고 다음 라운드가 곧바로 진행된다.
블록의 생성간격(block interval)은 약 1초이다.

![propose](http://drive.google.com/uc?export=view&id=1UoT5LYqzEcFvCtlqIBDSYYg_eNrNDZJf)

각 라운드 시작시 블록 생성을 위한 제안자를 무작위(randomly) 그리고 결정적(deterministically)으로 합의 노드(governance council)들 중에서 선택한다.
또한 각각의 합의 노드가 가장 최근에 블록 헤더에서 파생된 난수를 통하여 자신이 이 라운드에서 선택된 validator인지를 증명하기도 해야한다.
물론 공개키를 이용한 암호 증명을 제출하는 방법이다.

이후 합의 과정에따라 2/3이상의 서명을 받는 경우 블록을 생성하고 전파하게 된다.
전파는 프록시 노드를 통하여 엔드 포인트 노드들에게 전달하는 방식이다.

## 네트워크 구조

![network](http://drive.google.com/uc?export=view&id=1K_w8zu1mxrmT-50g1VQ_IcUu0fC7hs0Z)

기본적으로 Core Cell Network와 그것을 둘러싼 Endpoint Node Network(ENN)로 구분되어있다.

Core Cell Network는 다시 Consensus Node Network(CNN)과 Proxy Node Network(PNN)으로 구분된다.

CN은 단어 그대로 합의 를 담당하는 노드이며 하나의 Core Cell은 참여자 하나가 운영하는 단위로 CN 한개와 PN 다수로 이루어져 있다.
또한 CN은 각각의 모든 CN들과 연결되어있는 구조인데, 이는 합의 과정에서 빠른 의사표시를 위함이라고 볼수 있다.
CN은 외부와의 직접적인 연결을 하지못하는 비공개 영역이며 PN을 통하여 통신을 한다. 자신이 믿을수 있는 PN을 다수 두어 하나의 Core Cell을 이루는 것이다.

EN들은 Core Cell에 PN을 통해서 빠르게 신뢰성 높은 블록을 전달 받을수 있다. (물론 근처에 있는 EN을 통해서도 받을수 있으며, 각 EN은 웹이나 모바일 같은 형태로 서베스 제공자의 역할을 할수 있다.)

Boot Node는 새로등록된 노드들이 네트워크에 연결되도록 도움을 주는 특수한 노드들로 클레이튼에서 직접 운영하는 노드들이다.
CN BootNode는 CNN안에 있는 비공개 노드이며, PN BootNode와 EN BootNode는 공개되어있다.
EN Boot Node는 어떤 PN에 연결해야 하는지 EN들을 이끌어 주는 역할을 하며 PN BootNode는 허용된 PN만 등록되도록 관리하며 EN과의 연결을 돕는다.

## 코어셀(Core Cell)

개인적으로 쉽게 표현하면 하나의 검증자로 동작하기 위한 최소 단위라고 볼수 있을것 같다. (일반적인 상황에서는 특정 기능을 제공하는 서버역할)
블록체인이 아닌 환경에서는 사용자가 늘어나서 시스템에 부하가 생기면 서버를 늘리고  Request를 분산 처리하여 해결한다.
하지만 블록체인은 노드를 늘린다고 성능이 좋아지는 것이 아니며 오히려 늘린만큼 공유해야하는 노드수가 늘어나면서 합의과정이 느려질수 있다.
클레이튼에서는 노드 자체 성능을 늘리는 방법으로 해결한다. 전체적인 CN H/W스펙이 곧 클라이튼 네트워크 성능이다.

CN에 참여하기 위한 조건은 굉장히 까다롭다.
Physical Core 40개이상
256GB RAM
1년치의 데이터를 저장할 수 있는 14TB이상의 저장공간
10G 네트워크 

하지남 블록체인 특성상 하나의 노드가 특출나게 좋다고 해서 성능이 좋아지는 것이 아니다. 결국은 하향평준화 되어 느린 노드에 맞춰지게 된다.

코어셀에 PN을 두는 이유는 무엇인가?
당연히 이것도 성능과 연관이 있다.

![corecell](http://drive.google.com/uc?export=view&id=1yr2ZQjfXUR1fEsse67Y1UZ0K1_Iiv0fi)

CN의 동작에서 합의를 하기 까지 필요한 커넥션의 수는 많지않다. Validator들과의 통신만 하면된다.
다만 이런 결과를 공유까지 해아한다면 실제 EN 개수만큼의 잠재적인 커넥션 숫자를 감당할수 있어야 한다.

이는 합의를 하는 CN의 입장에서는 굉장한 부하 요소이다. 이를 위하여 외부와 통신을 위한 미들웨어 개념으로 PN이 추가된 것이다.
PN은 중간에서 EN의 커넥션을 받아주고 필요한 트랜잭션만 CN에 전달하는 방식으로 최소한의 부하를 주기 위함이다. 또한 EN이 증가함에 따라 PN도 같이 증가될수 있기 때문에 확장성의 문제점도 해결된다.


