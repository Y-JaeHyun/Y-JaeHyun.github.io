---
layout:     post
author:     bcnote3314
title: OS 환경 변수
subtitle:  성능을 올리기 위한 삽질 기록
category: Note
tags: 		OS Experiences
---

# 개요

Application의 성능을 결정하는 요인이 꼭 개발자의 코드에만 존재하는 것은 아니다.  
시스템의 여러 사양과 환경에따라 동일한 로직에서도 성능 차이가 발생할 수 있다.  

일부 설정 변경에 따라 성능차이가 발생했던 점을 기록해두고 해당 설정에 대한 이해를 하고자 한다.  
이런 환경은 서버의 환경에 따라 설정 방법이나 용어가 다를수는 있을것 같다.  
또한 설정끼리 서로 충돌(?)이 나서 사용 조합에따라 차이가 발생하기도 하기 때문에 이런 환경의 변경에 대해서는 충분한 시험과 검증이 필요한 부분이다.
 

# OS 관련 설정 

## 부트 로더 설정 (grub.cfg)

다수의 리눅스 서버에서 사용되는 부트로더가 GRUB이다.  
최근 OS의 경우 /boot/grub2/grub.cfg 파일을 통해서 다양한 커널 파라미터를 변경 가능하다.  

### isolcpu

코어 분리 설정, OS가 관리하는 스케쥴러에서 해당 옵션에 정의된 코어를 분리해버린다.  
즉 OS에 의하여 특정 스레드가 설정된 코어에 할당이 불가능하게 되며 직접적으로 할당한 스레드만 해당 코어를 사용하도록 할수 있다.  
성능에 Critical한 영향을 미치는 스레드들을 고정하여 Context switch에 의한 병목을 없애준다.  

이런 비슷한 동작을 Tuna라는 툴을 이용해도 비슷한 효과를 처리할 수 있다.  

### nohz_full

틱리스 커널을 사용하는 환경에서는 유휴상태의 코어는 인터럽트하지 않는다는 특징을 이용하는 것으로 보인다.  
동적으로 해당 코어를 틱리스 상태로 만들어서 OS에 의해 해당 코어가 간섭받는것을 줄인다는 의미 정도로 이해했다.  

### rcu_nocbs

RCU(Read-Copy-Update) 시스템은 Lockless 동기화 메커니즘이다.  (이건 또 나중에 따로 알아봐야 할듯..)
RCU 처리 과정 중에 어떠한 콜백 처리(동기화를 위한 뒷처리 정도로 이해하면 될듯..?)를 위해 코어가 대기 되는 경우가 있다.  
이러한 콜백처리를 위한 코어 목록에서 분리시키는게 rcu_nocbs옵션이다.  

추가로 Linux Portal에서는 분리된 코어가 자신의 RCU 콜백 처리 요청의 존재 여부를 인지하고 다른 코어에 전달할 의무룰 대신 처리하기 위하여 rcu_nocb_poll 이라는 옵션을 같이 활성화 하는것을 권하는 것으로 보인다.  
(영어실력이 암울해서 잘못 이해한것일 수 있다.)  
rcu_nocb_poll 옵션은 아직 적용해보지 않았기 때문에 우리 환경에서 어떤 차이가 생길지는 검증이 필요하다.

결론적으로 isolcpu, nohz_full, rcu_nocbs 세가지는 모두 OS에 의해 사용자의 Thread가 방해받는 것을 없애기 위한 옵션으로 이해하면 될것같다.

## idle

idle은 CPU의 유휴처리를 위한 옵션이다.  
CPU가 깨어나는 시간으로 인한 성능 저하 요소를 최소화하고 Context Switch 속도를 빠르게 하기 위한 옵션이다.  
아래 세가지 옵션값을 지정하여 동작을 정의한다.  

### intel_idle.max_cstate 

해당 옵션을 0으로 설정하면 cpuidle 드라이버를 비활성 하거나 acpi_idle로 되돌린다. (cpuidle 드라이버가 성능 이슈가 있던것 같다.)  

### processor.max_cstate

만약 intel_idle.max_cstate 옵션을 0으로 변경했을때 드라이버 비활성(cat /sys/devices/system/cpu/cpuidle 값이 none)인경우에는 해당 설정이 필요하지 않다.  
acpi_idle 드라이버에 대한 설정이지만 대부분의 환경에서 intel_idle.max_cstate 옵션을 0으로하면 비활성화 처리가 되기 때문에 확인이 어려운 옵션이다.  
CPU의 전력 전원 옵션(C-State)를 사용하지 않도록 한다.

### idle

실제 idle 상황에 동작을 정의한다.  

* poll : 전력과 온도를 사용하여 최대의 성능을 사용한다.
* halt : HLT 명령어를 통해 인터럽트가 발생하기 전까지 잠든다.   

참고사항 : 뒤에 설명할 Hyper Thread관련 옵션들과 영향이 있다. 각자의 시스템 환경에 따라 다른 영향이 있을수 있으니 반드시 시험과정이 필요하다.  



## Hyper Thread, Sibling Core 


## numa_balance

## transparent_hugepage

## sched_migration_cost_ns, sched_nr_migrate

# tund

