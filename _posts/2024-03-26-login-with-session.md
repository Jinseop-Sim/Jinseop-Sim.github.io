---
layout: post
title: "토큰 없이, 세션으로 로그인 구현하기"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
이전에 진행했던 토이 프로젝트 중 ```웹툰 추천 및 공유 서비스```가 있다.  
그 프로젝트를 한번 뜯어고쳐 보려고 한다.  

기존에는 ```Security```와 ```JWT```를 사용해 로그인 기능을 구현했다.  
하지만 이 떄는 제대로 알지 못하고, 주먹구구식으로 구현했었던 것 같다.  
그래서 이번에 ```Security```와 ```JWT```를 들어내려고 한다.  
완벽하게 이해하며 ```Session```을 이용해 로그인을 구현하려 한다.  

### 기존 방식
기존에는 아래와 같이 ```Spring Security```를 활용했다.  
```AccessToken, RefreshToken```을 모두 사용했음을 확인할 수 있다.  

<img width="815" alt="스크린샷 2024-03-26 오후 11 42 42" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/ddcc779b-f7b4-4ca3-8fcb-533bfb860675">  

이 부분을 모두 덜어내고 ```Session```을 사용해보도록 하자.  

### Session의 개념

### Session의 구현
