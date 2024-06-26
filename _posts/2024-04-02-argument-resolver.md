---
layout: post
title: "세션 리팩토링 (w. Argument Resolver, Redis)"
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

![argumentresolver drawio](https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/975d7920-e19d-42ee-a786-df529a2eb90b)

간단하게 위와 같이 도식화 해보았다.  
요청이 들어오면, ```DispatcherServlet```이 컨트롤 타워의 역할을 맡는다.  
사용할 ```Handler```를 매핑하고, 해당 ```Handler```를 끼울 ```Adapter```를 찾는다.  
이후 ```Adapter```를 호출해서, ```Model```에 값을 넣는 작업을 처리한다.  

이 때, ```RequestMappingHandler```에서 ```ArgumentResolver```가 참여하게 된다!  
쉽게 말해, ```Controller```에 들어가는 매개변수를 처리하기 위해 참여한다.  
```ArgumentResolver```가 매개변수를 가공해서 ```Controller```에게 넘겨주게 된다.  
그럼 ```Adpater```에서, 해당 매개변수와 함께 ```Controller```를 호출하게 된다.  

그럼 해당 ```Controller```에서 반환한 값을 ```ReturnValueHandler```에서 응답으로 내보낸다!  
여기까지가 ```HTTPRequest```를 처리해서 응답으로 반환하기까지의 과정이다.  
우리는 ```ArgumentResolver```가 처리할 HTTP 메시지 내에서, ```Session```을 이용할 것이다.  

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

### 세션과 서버 재시작
현재 코드를 계속 수정하며, 서버를 재시작 하는 경우 자동으로 로그아웃이 된다.  
사실 서버에 세션이 저장되기 때문에 어떻게 보면 당연한 것이다.  
사용자의 세션이 서버가 재시작되며 모두 초기화 되어 버리기 때문에, 로그아웃 되는 것이다.  

하지만 만약 실제 서비스를 하는 환경이라면?  
무중단 배포를 해놓고, 서버를 업데이트 한다고 생각해보자.  
하나의 서버를 업데이트 해야할 때, 로드밸런서를 통해서 구버전의 서버로 트래픽을 몰 것이다.  
업데이트가 끝나고 다시 로드밸런서를 돌려, 원래의 서버에 연결하려 한다면?  
서버가 재시작되었으니, 업데이트 된 서버는 세션이 초기화 되었을 것이다!  

그럼 사실상, 무중단 배포의 의미가 없어지는 것이다.  
```다운타임```을 없애고 사용자에게 연속된 서비스를 제공하기 위함이 목적인데,  
사용자는 갑자기 세션이 초기화 되어 다시 로그인 해야하는 경험을 해야 한다.  
이를 어떻게 방지할 수 있을까?  

#### Redis
나는 Redis를 이용하면 어떨까 생각해보았다.  
```RefreshToken```을 저장해서 인증에 사용했듯이, 세션도 저장하는 것이다.  
세션의 유효기간과 동일한 ```ExpireTime```을 설정하도록 해보자.  
그럼 세션이 유효기간이 만료되지 않았는데 사용자가 로그인을 못하는 경우를 방지할 수 있다.  

```Redis``` 내에 세션이 살아있는 동안은 지속적으로 인증이 가능한 것이다!  
또한, 조금 찾아보니 ```EnableRedisHttpSession``` 어노테이션이 지원된다고 한다.  
```Redis``` 내에서 직접 세션을 관리하도록 지원하는 것이다.  
이 기능을 한번 사용해보려고 한다.  

### 실제 구현
사실 내 프로젝트에는 굳이 필요가 없는 기능이긴 하다.  
그래도 한번 구현해보도록 하자.  

먼저 새로운 것을 도입할 땐, 항상 Configuration을 작성해주자.  
```EnableRedisHttpSession``` 어노테이션은 ```Configuration``` 어노테이션이 위치하는 곳에 둔다.  

<img width="873" alt="스크린샷 2024-04-03 오전 12 07 34" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/a959f3af-65fc-40d1-b1a5-3014bb55a1d1">  

위와 같이 ```maxInactiveInterval```을 설정해줄 수 있는데, 기본값은 ```30```분이다.  
```Session```의 만료 기간을 설정하는 것이다.  
그 결과, 로그인을 하게 되면 아래와 같이 ```Session```이 저장됨을 확인할 수 있다.  

<img width="528" alt="스크린샷 2024-04-02 오후 11 49 11" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/8fd16c15-054c-429e-8571-4010d4636cd8">  

서버를 재시작하더라도 로그인이 잘 되어있음을 확인할 수 있었다.  
어노테이션 하나만 추가했는데, ```Session```이 자동으로 저장되는게 신기하다.  
이렇게 간편한 기술일수록, 어떻게 동작하는 지를 알아야 한다고 생각한다.  
```EnableRedisHttpSession```에 대해서 공부를 좀 더 해보도록 하자.  
