---
layout: post
title: "세션 리팩토링 (w. Argument Resolver)"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
현재 리팩토링 중인 프로젝트에서는 사용자 인증을 위해 세션을 사용 중이다.  

이전 포스팅에서 세션 관련된 함수들을 리팩토링 한 기록을 남겼었다.  
아래와 같이 ```SessionUtil```을 만들어 코드 중복을 피하도록 했다.  
각 ```Service``` 단에서 함수를 호출해서 사용토록 한 것이다.  

<img width="703" alt="스크린샷 2024-04-02 오후 3 16 16" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/9133a4f2-2170-4331-9e62-283d11c32e93">  

물론 지금도 코드 중복을 많이 줄여놓은 상태지만, 더 줄일 수 있다면?  
별다른 함수의 호출 없이도 깔끔하게 코드를 짤 수 있다면?  
그럴 때 필요한 것이 바로 Spring에서 제공하는 ```ArgumentResolver```이다!  

### ArgumentResolver?
이름에서 알 수 있듯이 ```Argument```, 즉 매개변수에 관한 인터페이스이다.  
