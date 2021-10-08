---
layout:     post
author:     bcnote3314
title:  성능을 올리기 위한 삽질 기록 (정리중)
subtitle: OS 환경 변수
category: Note
tags: 		OS Experiences
---

# 개요

Application의 성능을 결정하는 요인이 꼭 개발자의 코드에만 존재하는 것은 아니다.  
시스템의 환경 설정에 따라 동일한 로직에서도 성능 차이가 발생할 수 있다.  

일부 설정 변경에 따라 성능차이가 발생했던 점을 기록해두고 해당 설정에 대한 이해를 하고자 한다.  


# OS 관련 옵션

## isolcpu, nohz_full, rcu_nocbs

## idle=poll, Hyper Thread, Sibling Core 

## numa_balance

## transparent_hugepage

## sched_migration_cost_ns, sched_nr_migrate

# tund

