---
layout:     post
author:     bcnote3314
title: 	IPS Management Process 개발
subtitle: WINS
category: Experiences
---

## 소개

* 네트워크 보안장비를 관리를 위한 SNMP Process의 신규 개발
  
  
## 작업 기간

* 2017.06 ~ 2017.08
  
  
## 진행 내용

* SNMP MIB(Management Information Base) 재정의
* 데이터 수집 모듈 개발
  * OS 상태 정보 (메모리, CPU 사용량 등)
  * IPS 데몬 상태
  * Power, Fan, 장비 온도 정상 여부
  * NIC 카드 정보 (Hang up, Link Down 등)
  * 트래픽 환경에 따른 특이사항 (Packet Drop, Overflow 등)
* net-snmp 오픈소스를 활용하여 모니터링 시스템 요청에 따른 응답 에이전트 개발
* 상태 이상 알람 프로세스 개발
  * snmptrap, syslog, Mail로 고객에게 알람 전달
  
  
## 성과

* 프로세스 개발 완료 및 릴리즈   
  
  
## 기술

* C, Python
* SNMP, Syslog