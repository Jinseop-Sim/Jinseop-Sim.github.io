---
layout: post
title: "랭킹 서비스 개발 2 (w. Redis)"
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
조회수 중복을 방지하기 위해서는 어떤 방법을 적용할 수 있을까?  
가장 간단하게 생각할 수 있는 것은 ```DB```에 체크하는 것일것이다.  
각 게시글들이 ```DB```에 조회한 ```User```를 가지고 있는 것이다.  
그럼 지금 로그인한 유저가 연관되어 있는지 조회 후, 방지를 할 수 있을 것이다.  

하지만 이 방식은 성능 저하를 초래할 확률이 너무 크다.  
조회를 할 때 마다 ```Update``` 쿼리가 날아갈 것이기 때문에, 성능이 저하된다.  

#### 쿠키
검색을 조금 해보니, 쿠키나 세션을 이용하는 방법들을 볼 수 있었다.  
쿠키를 

#### Redis

<img width="587" alt="스크린샷 2024-04-07 오전 3 16 03" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/a2a19277-d3c1-4ccb-bfd7-5277b84254a4">  

<img width="736" alt="스크린샷 2024-04-07 오전 3 15 45" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/2aa096ca-2d21-486c-8301-48fbe2457d63">  

### Spring Scheduler
