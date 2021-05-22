---
layout:     post
title:      Klaytn(클레이튼) Application 만들기 - 7(완)
author:     bcnote3314
tags: 		blockchain Klaytn 클레이튼
subtitle:  블록체인 정복기	
category: Block
---

# 클레이튼으로 Bapp 만들기 

클레이튼 앱을 이용하기 위해서는 wallet에 접근하듯 로그인을 하는 과정에서 계정인증이 필요하며, caver-js를 통하여 Klaytn네트워크와 통신을 해야한다.
또한 게임이나 기타 어플리케이션이 동작하기 위한 기본적인 웹 서버 로직이 필요하다.

## node.js(javscript), jquery, HTML 헬파티

대학교때 잠깐 해보긴 해봤던 것들이지만 오랜만에 보려니 너무 어렵다. 바뀐것도 많고.  
강의에서 하는것들을 따라해가면서 강의때와 현재 변경된 내용들은 따로 수정해 가면서 해보았다.

HTML이나 기타 다른 config 요소는 크게 리뷰할 의미가 없거나 환경에따라 차이가 많고,
로직이 어려운것은 아니기 때문에 내가 겪은 삽질에 대한 부분만 리뷰하고 마무리 해야겠다. 
블로그 정리하는게 익숙하지가 않아서 이런건 어떤식으로 작성하는게 좋은지 잘 모르겠다. 점점 정리하는게 나아지길 바랄뿐..

## 삽질 1 - 환경 설정

당연한 절차인것 같지만 환경설정하는데 삽질을 오래했다.

nodejs, npm을 설치하고 관련 모듈을 install 하려는데 python이 설치 안되어 있어서 오류가나고;  
path가 자동으로 추가되는 프로그램이 있는 반면 아닌것들도 있어서 명령어를 못알아 듣는 경우도 있고

다되었나 싶어서 코딩좀 하다보니 truffle이 말썽이고 아무튼 역시 환경설정은 항상 어려운거같다.  
회사에 입사하고나서 대부분의 일을 linux환경에서 했기 때문에 오히려 windows에서 설정하는게 더 어렵게 느껴졌다.

그래서 필살기를 썼다. Linux Bash(Ubuntu) 깔아서 해버렸다..

![리눅](http://drive.google.com/uc?export=view&id=1cqlwpUBJzRweUVtxjuzS-rWuop4Yjpld)

다만 이게 문제점이 visual studio에서 지원하는 터미널을 이용하려면 윈도우에서 셋팅이 되어야 할건데 야매로 하다보니까 매번 터미널에서 서버 실행시키거나 스마트 컨트랙트 배포 작업 같은것을 할때 따로 터미널을 열어줘야하는건 단점이다.
truffle, node 등은 아마 다른 블록체인을 공부할때도 필요할것 같으니 윈도우에서도 정상적으로 가능하도록 다시 수정해야한다.

나중에 필요할수도 있으니 환경설정 이후 주로 사용하는 명령어만 정리한다.

1. truffle
- truffle deploy --network klaytn : 스마트 컨트랙트 최초 배포
- truffle deploy --compile-all --reset --network klaytn : 기존 배포 내용 reset후 재배포
1. npm 
- npm install : package.json에 정리된 dependency에 맞춰 모듈 다운로드
- npm run dev : webpack으로 테스트용 웹서버를 띄우는 명령으로 package.js에 정의되어있다. (나중에 따로 webpack을 공부해봐야 할듯하다.)

## 삽질 2 (아직 미해결)
    
```solidity
	/*
    if (await this.callOwner() === walletInstance.address) {
      $('#owner').show();
    }
    */

    if (walletInstance) {
        //if (await this.callOwner() !== walletInstance.address) return;
        if (0) return;
        ....
    }
```

위 로직은 contract의 생성한 계정에서만 특정 로직을 수행하기 위한 예외처리 로직이다.  
크게 이상할것은 없어보인다. 하지만...

![콘1](http://drive.google.com/uc?export=view&id=1CERrKjjSEDsuNNGxYg3R_PLJdElQYIuF)

![콘2](http://drive.google.com/uc?export=view&id=1ryMyyR2uUIgx97VHBasWLBaZr91G2-PO)


....?

분명 console.log를 보면 두개의 값은 같은 값이다.  
해당 결과값을 그대로 === 비교해보면 true라고 나온다.  
하지만 실제 비교값은 false이다. 왜!!!!!

await / async는 비동기 방식의 처리를 지원하는 예약어기 때문에 비교하는 시점에 결과값이 없는 상태이기 때문일까?
그렇다면 비동기 값과 일반 변수는 비교를 할수 없는건가?

이건 자바스크립트를 공부해야 알수 있는 문제인것 같다. 우선 고민거리로 냅둬야 할것같다... ㅠㅠ

## 삽질 3

```solidity
    //import {clearInterval} from "timers";

	var interval = setInterval(()=>{
        $('#timer').text(--seconds);
        if (seconds <= 0) {
            $('#timer').text('');
            $('#answer').text('');
            $('#question').hide();
            $('#start').show();
            console.log(seconds);
            clearInterval(interval);
        }
    }, 1000)
	
```

[해결 도움 : 스택 오버플로우](https://stackoverflow.com/questions/48046977/error-timeout-close-is-not-a-function-when-try-to-clear-interval-angular5)

원인은 import 때문에 발생했다.
IDE상에서 자동으로 가져오는것과 충돌(?)이 나는 것으로 이해헀다. 제거하기 전에는 clear동작이 정상적으로 되지 않아서 아래와 같은 에러를 남발했다.

![클리어](http://drive.google.com/uc?export=view&id=1q5rta-JIIWKMcskAcDKKZp_RHELIseUW)

##  강의 후기

기본적으로 klaytn에 개념을 잡기는 나쁘지 않은 강의인것 같다.
개념적인 부분은 이해하기 쉽게 설명해주고 있고 코드에 대한 부분도 초보자가 접근하기에 크게 어렵지는 않았다.  
중간에 변경된 부분에 대한 갱신은 없지만(baobab 링크나 receipt의 내용도 조금씩 달라진 부분이 있어서 실제 코드상에서 변경이 필요한 부분이 있다.) 추적하기 어려운 부분은 아니기 때문에 이해하는데 크게 어려움은 없다.
다행인지 삽질의 원인이 다 klaytn 외적인 것들이였다는것.

대부분의 서비스가 웹으로 제공되기 때문에 역시나 웹에 대한 공부를 하긴 해야할것 같다. (물론 코어적인 부분으로 들어가면 상관없겠지만, 아직은 좀 어려울것같다는 막연한 두려움이..ㄷㄷ)  
caver에 대한 부분도 따로 API 문서를 통해서 보면 사용 방법에 대해서는 크게 어렵지는 않은 느낌이다. 기존 웹 개발자들은 쉽게 적응할수 있을것 같은 느낌이다.

다음 post부터는 마스터링 이더리움에 대한 내용정리를 진행할 예정이다. 
번외로 Application 개발 강의가 끝났으니 이를 이용한 간단한 서비스를 구현해볼만한 것이 없을지 고민해보고 그에 대한 개발 과정도 정리해볼 예정이다.
