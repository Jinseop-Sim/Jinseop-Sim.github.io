---
layout: post
title: "랭킹 서비스 개발 (w. Redis)"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
저번에 이어 랭킹 서비스를 마저 개발해보려고 한다.  
내가 이제 해결해야 할 과제는 아래의 두 개와 같다.  

1. 회원 한 명당 각 게시글에 대해 조회수는 한 번만 올릴 수 있다.
2. 하루가 지나면 자동으로 ```Sorted Set```을 비울 수 있어야 한다.

1번부터 먼저 구현해보도록 하자.  

### 조회수 중복 방지

### Spring Scheduler
