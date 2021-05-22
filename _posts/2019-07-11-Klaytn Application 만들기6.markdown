---
layout:     post
title:      Klaytn(클레이튼) Application 만들기 - 6
author:     bcnote3314
tags: 		blockchain Klaytn 클레이튼
subtitle:  블록체인 정복기	
category: Block
---
# 클레이튼으로 Bapp 만들기 - 프론트엔드!

기본적인 스마트 계약 작성이되었으면 이를 응용하여 사용자가 이용할수 있는 UI를 제공해주어야 한다.
Bapp 개발에서 UI/UX는 굉장히 중요한 영역이다.

일반 사용자들은 블록체인 환경을 잘 모르기 때문에 일반적으로 사용하는 App처럼 쉽게 접근할수 있는 UX를 제공해주는 것이 현재 Bapp/Dapp 개발자의 미션중의 하나이다.

자바스크립트에 대한 이해가 부족하기 때문에 자바스크립트 관련 Post도 따로 공부할겸 작성해야하나 고민이다.. 후 볼게 많네..
이래서 코어부분은 언제보나 싶다..

## 기본적인 프론트엔드

강좌에서는 아래의 것들을 통하여 구현한다.

1. Node.js (NPM)
1. 트러플 프레임워크 
1. 비쥬얼스튜디오 코드

truffle에는 기본적인 템플릿을 제공한다.

[트러플 슈트](https://www.trufflesuite.com/boxes)

프론트엔드에서 사용하려는 각종 언어(react, angular등)에 맞는 템플릿들이 제공되는 장점이 있지만 현재 대부분은 이더리움을 기반으로 하기 때문에 클레이튼에 맞추어 일부 수정할 필요는 있다.
해당 강좌에서는 webpack 기준으로 만들어져있다.

## 구조 설명

1. contract

solidity contract 파일들이 보관된다.
이전에 만든 AdditionGame.sol과 같은 작성한 스마트 계약을 보관하며 Migration.sol 이라는 컨트랙트는 배포시에 migrations안에 스크립트 파일들을 실행하도록 해준다.
스크립트 파일은 배포시에 실행하는 로직이 작성되어있다.
컨트랙트 파일을 불러와서 클레이튼 노드에 deploy하는 형태로 작성되어있다.

2. src

실제 프론트엔드 소스가 작성되는 부분이다. 
index.html이 기본 화면 작성부분이며, index.js는 실제 기능을 담당하는 jQuery 내용이 작성될 예정이다.

3. 그외
- package.json 
의존성이 있는 각종 정보들이 포함되어있다. (java에서 maven이 하는 역할인듯)
caver-js(이더리움이라면 web3.js)등을 자동으로 받아준다. 
- truffle.js
환경설정을 담당한다. 어떤 네트워크에 계약을 배포할지 등을 선택한다.
-webpack.config.js
파일을 최적화시켜주고 코드에 변화가 있으면 브라우저에 변경사항을 반영해주는 역할 등을 한다.

## 배포 방법

migrations에 배포를 위한 스크립트를 만들어야한다.

```solidity
    const fs = require('fs')
    const AdditionGame = artifacts.require('./AdditionGame.sol')
    
    module.exports = function (deployer) {
      deployer.deploy(AdditionGame)
        .then(()=>{
            if (AdditionGame._json) {
                fs.writeFile('deployedABI', JSON.stringify(AdditionGame._json.abi),
                    (err) => {
                        if (err) throw err;
                        console.log("ABI INPUT SUCCESS");
                    } 
                )
                fs.writeFile('deployedAddress', AdditionGame.address,
                    (err) => {
                        if (err) throw err;
                        console.log("Address INPUT SUCCESS")
                    }
                )
            }
        })
    }
```

해당 코드는 AdditionGame이라는 계약을 배포하기 위한 소스이다.
기본적으로는 deploy 수행하는 로직만 있으면 되지만. 추후 활요을 위한 정보를 따로 파일에 갱신해주는 로직을 추가한 것이다.

배포코드 이후엔 truffle에 대한 설정이 필요하다.

```solidity
    // truffle.js config for klaytn.
    const PrivateKeyConnector = require('connect-privkey-to-provider')
    const NETWORK_ID = '1001' //바오밥
    const GASLIMIT = '20000000'
    const URL = 'https://api.baobab.klaytn.net:8651'
    const PRIVATE_KEY = '0x---------------------------------------------------------------'
    
    module.exports = {
        networks: {
            klaytn: {
                provider: new PrivateKeyConnector(PRIVATE_KEY, URL),
                network_id: NETWORK_ID,
                gas: GASLIMIT,
                gasPrice: null,
            }
        },
    }
```

클레이튼 테스트넷인 바오밥에 배포를 하는 내용이다. 기본적으로 가스는 알아서 채워주기 때문에 null을 넣어두어도 무방하다.  
완료이후 truffle deploy --network klaytn 명령을 통하여 배포가 가능하다.  
만약 수정사항에 대한 재배포가 필요시 --compile-all --reset 옵션을 통하여 다시 deploy를 하도록 해야한다.

