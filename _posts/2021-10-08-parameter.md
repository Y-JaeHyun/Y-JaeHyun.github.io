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

## Hyper Thread

CPU 스펙을 보면 4코어 4스레드, 4코어 8스레드 등의 표현을 쓴다.  
여기서 코어란 진짜 물리적인 반도체이다.  
스레드는 OS에서 인지하는 논리적인 Core 개수이다. (프로그래밍 관점의 스레드와는 당연히 다른의미다.)  

OS에서는 스레드의 개수를 실제 물리적인 코어 개수인것처럼 인식하여 동작을 하게 된다. 이를 구현한 기술이 하이퍼 스레딩이며 병렬처리를 위한 것이다.  
단순하게 설명을 보면 무조건적으로 하이퍼 스레드 옵션을 사용하는 것이 좋을 것으로 보인다.  

하지만 상황에 따라 오히려 성능을 저하시키는 요인이 될수도 있다.  
예를들어 내가 시험했던 환경에서는 idle=poll 과 Hyper Thread가 동시에 사용되면서 성능이 저하되는 현상이 있었다.  

논리적으로 구분되더라도 결국 하나의 물리 프로세서를 사용하기 위하여 싸우기 될 것이며 idle polling 처리 과정에서 두개의 스레드가 경쟁하면서 발생하는 문제였다.  

코어를 ON/OFF 시키기 위해서는 아래 명령어를 통해 가능하다.  

0번 코어 활성화 or 비활성화
echo 1 > /sys/devices/system/cpu/cpu0/online
echo 0 > /sys/devices/system/cpu/cpu0/online


# tuned

tuned는 프로파일설정을 통해 운영체제가 특정 작업에서 나은성능을 발휘할수 있도록 제공되는 데몬이다.  
이미 정의된 프로파일들이 몇가지 존재하며 사용자가 새로 정의하여 사용할 수도 있다.  

기본으로 정의된 프로파일은 아래와 같다.  

1. throughput-performance : 처리량 개선에 중점을 두었으며 대부분의 기본 설정되어있다.  
2. latency-performance : latency를 낮추는데 중점을 두었다.  
3. network-latency : 네트워크 대기시간 단축에 중점을 두었다.  
4. network-throughput 네트워크 처리량에 중점을 두었다.  
5. virtual-guest : 가상 머신 성능 최적화에 중점을 두었다.  
6. virtual-host : 가상 호스트에서 성능 최적화에 중점을 두었다.

각 프로파일 설정에따라 위에서 봤던 절전 관련 옵션이나 기타 커널의 환경 설정들을 셋팅하며 상황에 따라 또 다른 설정을 통해 본인의 환경에서 가장 좋은 결과를 도출해 내면된다.  

내 경우에는 무조건적으로 latency가 중요했기 때문에 latency-performance와 network-latency 프로파일에서 설정하는 옵션들을 수정해 가면서 최적의 조건을 맞추려고 했었다.  

## numa_balance

[NUMA](https://y-jaehyun.github.io/note/2021/09/13/numa/) 에 대해서는 메모리 관련 성능 저하 포인트를 분석하면서 본적이 있다.  
NUMA 구조에서는 당연히 자신의 Local Memory 사용하는 것이 성능적인 이득이 있으며 Remote Memory 접근 과정에서는 높은 latency가 발생한다.  

numa_balance 옵션은 remote에 존재하는 메모리를 커널단에서 자동으로 local로 이동시켜주는 옵션이다.  

하지만 tuned에서 network-latency 프로파일에서는 numa_balance를 disable 시킨다.  
의도하지 않은 메모리 이동과정에서 발생하는 지연시간을 없애기 위한 의도로 보인다.  

이 옵션은 disable로 바꾸고 성능상 중요한 메모리 접근은 대부분 지역변수 내에서 처리하거나 TLS변수로 변환하는 등의 작업을 통해 더 좋은 효과를 볼수 있었다.  

## transparent_hugepage  

이 옵션은 [TLB](https://y-jaehyun.github.io/note/2021/10/04/TLB/) 연관이 있는 옵션이다.  
Linux 환경에서는 메모리에 대한 관리를 Page단위로 처리하며 Page는 기본적으로 4K의 크기를 가진다.  
하지만 메모리 크기가 늘어나게 되면서 데이터 처리를 위해 확인해야하는 Page의 개수도 늘어나게 된다.  
Page개수가 많아지면 자연스럽게 TLB내에 공간이 부족해지고 TLB Cache Miss 발생 빈도가 늘어나며 성능의 저하가 발생한다.  

이를 해결하기 위한 방법은 Page의 크기를 늘려셔 하나의 Page에 더많은 데이터가 들어가면 TLB에 사용되는 Page 개수를 줄일 수 있다.  
그것이 Huge Page이다.  

HugePage는 커널 파라미터에 지정하여 재부팅하거나 관련 설정들을 해줘야 하는데 이를 자동으로 관리 하기 위한 옵션이 Transparent Huge Pages (THP)이다.  

하지만 THP의 기존 의도와는 다르게 시스템 성능 저하를 유발하는 경우가 많아 Redis, MongoDB, Oracle 등 다수의 Database 관련 시스템에서는 해당 옵션은 Disable을 권장한다.  


## sched_migration_cost_ns, sched_nr_migrate   

해당 옵션들도 영향은 성능상 약간의 차이는 보였다.  
하지만 아직 이해는 잘 안된다...

이 두가지 말고도 kernel단에 설정하는 옵션들이 다수 있다.
CPU의 스케쥴링에 관련된 옵션들로 보이기 때문에 분명 인지하고 있어야 하는 옵션들임에는 틀림없다..


[kernel 관련 옵션 설명](https://doc.opensuse.org/documentation/leap/archive/42.1/tuning/html/book.sle.tuning/cha.tuning.taskscheduler.html)



