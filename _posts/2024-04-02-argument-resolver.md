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
```HTTP``` 요청이 들어왔을 때, 함꼐 들어온 파라미터를 가공해주는 역할을 한다.  
이후 ```Controller```에 매핑되는 파라미터를 찾아 가공된 값을 넘겨준다.  

정확히 이 개념을 이해하려면 ```DispatcherServlet```에 대한 개념부터 알아야 한다.  
REST API 요청이 들어왔을 때, 처리하는 흐름을 알아야 하는 것이다.  

### 실제 구현
<img width="743" alt="스크린샷 2024-04-02 오후 3 46 40" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/9507b10e-b99d-4173-9468-8a07a761f66a">  

<img width="272" alt="스크린샷 2024-04-02 오후 3 46 53" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/403614f1-3f9e-4671-bb7e-adf422b996d0">  

<img width="702" alt="스크린샷 2024-04-02 오후 3 47 12" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/8804fee2-e43b-428e-bc4f-a8e1bfb5b3a1">  

<img width="674" alt="스크린샷 2024-04-02 오후 3 57 13" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/f2b20cc9-88c8-4de1-a122-5bb190580d7b">  

<img width="835" alt="스크린샷 2024-04-02 오후 3 58 11" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/7b43fd41-06b8-46a9-9581-3c77984f8b87">  

<img width="549" alt="스크린샷 2024-04-02 오후 3 59 13" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/9258d2b7-335d-4c09-b271-38743f60311b">
