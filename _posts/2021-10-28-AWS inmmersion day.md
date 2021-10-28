---
layout:     post
author:     bcnote3314
title: AWS immersion Day
category: Cloud
tags: 		AWS
---

# AWS

IPS 가상화 관련 프로젝트를 진행하면서 Private Cloud가아닌 Pubillc Cloud에서도 동일한 서비스를 제공해야했다.  
Docker 컨테이너 기반으로 프로젝트를 진행했기 때문에 AWS EC2위에 도커를 올리고 IPS를 동작시키는 형태는 가능했다.  

다만 기존에 ovs-dpdk, vhost등 Application 밑단의 네트워크 구성등은 일부 변경이 있었다. 
ASW를 조금 만지작 거리다 보니 AWS에서 제공하는 다양한 기능, 서비스 들이 눈에 보였고 이에 관심이 생겨 AWS에서 진행하는 교육을 들어봤다.

사실 서비스의 개념보다는 전체적인 서비스 아키텍쳐 구성에 대해서 배울수 있는 좋은 시간이였던것 같다.  

그중에 일부 내가 자주 사용할것 같은것에 대해서 좀 정리해 둔다.  

아래 실습자료는 참고하면 도움이 많이 될것같다.  

[AWS - 실습 자료](https://kr-id-general.workshop.aws/ko/advanced_modules.html)

# Amazon VPC

Virtual Private Cloud, 가상의 네트워크를 구성할수 있도록 도와준다.  
인터넷에서 서비스를 제공하기 위해서는 당연히 공인 IP가 필요하다.  
공인 IP는 유한한 것으로 아마존에서는 공인 IP에대해서는 할당을 받고 사용하지 않아도 요금을 책정한다.  

즉, 다수의 공인 IP를 사용하는 것보다는 네트워크 구성을 이용하여 최소한의 비용으로 서비스를 구축하는 것이 중요할 수 있다.  

VPC는 크게 Public 영역과 Private 영역으로 구분된다.  
Public 영역은 IGW(Internet Gateway)을 통해 외부 인터넷과 통신이 가능한 영역이다.  
Private은 기본적으로는 내부에 숨겨져있어서 인터넷 통신이 불가능하며 VPC 내의 자원끼리의 통신만 가능하다.  
(하지만 뭐 당연하게도 불가능한것은 아니며 NAT Gateway 통해 인터넷 접근을 허용할 수는 있다.)

Public/Private을 나누는 기준은 다른 것이 아니라 결국 라우팅 테이블이다.  
IGW로 향하는 정책이 있으면 결국 Public이고 없으면 Private다.  

VPC 설정을 하다보면 Availability Zone(가용영역)이 나온다.  
하나의 리전에는 두개 이상의 가용영역이 존재하며 이들은 모두 격리되어있지만 서로 연결되어있다.  
하나의 가용영역에서 장애가 발생하면 다른 가용영역의 인스턴스에서 요청에대한 처리를 진행하게 된다.  

즉, 고가용성을 위한 HA구성으로 Subnet을 추가 구성하여 두개의 존에서 내 VPC 인스턴스들을 동작 시킬수 있다.
Subnet을 각기다른 Zone에 추가 구성하게 되면 라우팅 테이블을 통해 네트워크 구성을 해줘야 한다.

# Amazon EC2

..


