---
layout:     post
title:      Klaytn(클레이튼) Application 만들기 - 5
author:     bcnote3314
tags: 		blockchain Klaytn 클레이튼
subtitle:  블록체인 정복기	
category: Block
---

# 클레이튼으로 Bapp 만들기 - 스마트 계약!

간단한 덧셈게임을 통하여 정답을 맞춘 사용자에게 보상을 주는 Bapp의 개발.
그중 블록체인을 통한 스마트 계약 부분에 대한 부분이다.

## 문법

solidity를 처음 접하게되니 생소한 느낌이 많이든다.
나중에 기회가되면 solidity만의 새다른 문법이나 예약어정도는 정리해보는것도 나쁘진 않겠지만 우선 Bapp의 개발 과정을 훑어보는 정도로만...

[크립토좀비](https://cryptozombies.io/ko/)

크립토 좀비와 같은 플랫폼을 이용하면 좀더 빠르고 쉽게 적응되는거 같으니 보고 나중에 따로 리뷰정도만 남겨보자.

## 스마트 계약 코드

```solidity
    // Klaytn IDE uses solidity 0.4.24, 0.5.6 versions.
    pragma solidity >=0.4.24 <=0.5.6;
    
    contract    AdditionGame {
        address public owner;
        
        //생성자
        constructor() public {
            owner = msg.sender;
        }
        
        //현재 Contract에 존재하는 잔액
        function getBalance() public view returns (uint) {
            return address(this).balance;
        }
        
        //owner 계정에서 Contract로 송금
        function deposit() public payable {
            require (msg.sender == owner);
        }
        
        //정답을 맞춘 사용자가 함수를 호출하면 잔액 조회후 송금
        function transfer(uint _value) public returns (bool) {
            require(getBalance() >= _value);
            msg.sender.transfer(_value);
            return true;
        }
    }
```

## 코드 설명

덧셈게임. 

- 관리자(즉 스마트 계약을 배포한 계정)는 deposit 함수를 호출하는 것으로 상금을 넣을 수 있다.
- 관리자임을 판단하기 위하여 최초에 생성자가 호출될때 해당 생성자를 호출한 계정을 owner 변수에 저장해두며 추후 해당 owner와 함수를 호출한 msg.sender와 비교하는 방식
- 나중에 웹에서 처리되는 로직에 의하여 정답을 맞춘 사용자가 transfer 함수를 호출하게 되는 구조로 개발될 예정이며 transfer 함수에서는 잔액 비교후 호출한 상대에게 정해진 금액을 전달한다.

## 소스 테스트

테스트는 IDE를 통하여 모두 가능하다.
방식은 테스트넷인 바오밥에 트랜잭션을 발생시켜 결과를 확인하는 방식이다.

실제 트랜잭션 정보가 IDE에 출력되며 함수 형태에따라 매개변수를 입력하는 창이나 버튼등이 제공된다.

![4](http://drive.google.com/uc?export=view&id=138zrRNGsyuca83dA-8ihfxxKytr_0r_x)

![2](http://drive.google.com/uc?export=view&id=1VZtbqy-OOlCYEUPrUkuC6pgfv99Cb1S4)

![3](http://drive.google.com/uc?export=view&id=15kjhrKysvnWrW7cghqVCtPocP9t9PxSh)

주의해야할 내용으로는 클레이의 단위를 조금 조심할 필요가있다.
스마트 계약내에서 처리되어 출력되는 모든 결과물은 최소단위인 peb값으로 제공된다.

또한 매개변수등으로 거래와 관련된 처리를 진행할 때에도 당연히 이 최소단위 값을 기준으로 해야한다. (이때 테스트를 위해서 지난 post에 공유된 단위 변환 사이트를 이용하면 좋다.)


## 아쉬운점

내가 잘몰라서 드는 생각인건지 무료강의라 간단하게만 한건지 아직 이해는 잘안가지만 블록체인의 모든 내용은 공개된 정보라고 알고 있다.  
즉 해당 소스도 배포되고나면 공개된 소스인 것이다. (실제 트랜잭션이 바오밥에 의하여 진행된 내용이기 때문에 조회가 가능하다. 거기에 아직 열리진 않았지만 소스가 공개되는 것을 확인할수 있다.)

![1](http://drive.google.com/uc?export=view&id=1N3f03k524uHeiDUJh4Q9r05J0D6VES85)

위와같이 코딩을 하게되면 transfer라는 함수의 구현내용은 당연히 공개가 된것이고 관리자가 금액을 넣는 족족 transfer 함수를 호출해서 돈을 훔쳐갈 것이다.
간단하게 접근하는 것도 좋지만 너무 단순하게 마무리 하지말고 조심해야 하는 점이나 방어 방법 등에 대한 소개도 간략하게나마 포함되었으면 더 좋은 강의가 되지 않았을까 싶다.

transfer 수행 이전에 정답을 맞췄는지 인증하는 과정등이 포함되어야 할것 같다는 조그마한 의견을..ㅎㅎㅎㅎ


	
