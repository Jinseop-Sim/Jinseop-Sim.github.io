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
아래와 같이 ```UserArgumentResolver```를 구현해 주었다.  
Spring에서 제공하는 ```HandlerMethodArgumentResolver```를 구현한 구현체이다.  

<img width="946" alt="스크린샷 2024-04-02 오후 4 04 51" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/3fea59cc-910a-4535-9454-c86bfaa02515">

```supportsParameter()```와 ```resolveArgument()```를 오버라이드 했다.  
```supportsParameter()```는 파라미터의 타입에 대한 검증 장치이다.  
```resolveArgument()```는 본격적으로 파라미터를 가공해주는 함수가 되겠다.  

로그인할 때, ```Session```에 ```SessionConstant```를 키로 사용자 정보를 저장했었다.  
```ArgumentResolver```에서 사용자의 요청을 가로채, 저장했던 요청자의 정보를 꺼내보는 것이다.  
```getAttribute(SessionConstant)``` 메서드를 통해서 저장했던 정보를 꺼낸다.  
이후, 미리 만들어둔 아래와 같은 ```UserSessionDto```를 통해 정보를 반환한다.  

<img width="272" alt="스크린샷 2024-04-02 오후 3 46 53" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/403614f1-3f9e-4671-bb7e-adf422b996d0">  

커스텀한 ```ArgumentResolver```를 이제 기존의 처리 과정에 삽입해주어야 한다.  
아래와 같이 ```WebMvcConfigurer```를 구현함으로써 삽입이 가능해진다.  
```ArgumentResolvers```를 매개변수로 받아온 뒤, 내가 만든 ```resolver```를 ```add```한다.  
요청을 처리하는 기차가 지나가는 중간에 내 ```resolver```를 태우는 느낌같다.  

<img width="702" alt="스크린샷 2024-04-02 오후 3 47 12" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/8804fee2-e43b-428e-bc4f-a8e1bfb5b3a1">  

### 서비스 적용
이제 기존에 로그인 한 사용자의 정보를 불러와야 하는 서비스들을 수정해보자.  
기존에 있던 서비스 중 ```마이페이지```에 접근하는 서비스를 보도록 하자.  

<img width="674" alt="스크린샷 2024-04-02 오후 3 57 13" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/f2b20cc9-88c8-4de1-a122-5bb190580d7b">  

기존에는 위와 같이 ```SessionUtil```의 함수를 호출해서 정보를 받아왔다.  
하지만 아래와 같이 코드를 좀 더 깔끔하게 수정할 수 있었다.  
```ArgumentResolver```를 통해 넘겨받은 ```UserSessionDto```를 이용했다.  
별도의 함수 호출도 없고, ```SessionUtil```이 지금 당장은 필요 없게 되었다!  

<img width="835" alt="스크린샷 2024-04-02 오후 3 58 11" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/7b43fd41-06b8-46a9-9581-3c77984f8b87">  

아래와 같이 로그인을 하지 않고 접근하는 경우, 예외를 잘 응답함을 볼 수 있다.  

<img width="549" alt="스크린샷 2024-04-02 오후 3 59 13" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/9258d2b7-335d-4c09-b271-38743f60311b">  

또한 로그인 후에 마이페이지에 접근하는 경우, 아래와 같이 잘 응답됨을 볼 수 있다.  

<img width="485" alt="스크린샷 2024-04-02 오후 4 48 29" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/3dc706d8-8c0e-4d8a-8fed-bdf1d79e735c">  

남은 ```Session```을 사용하는 서비스들도 모두 수정해보도록 하자!  
