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
기본적으로 ```HTTP```는 ```STATELESS```한 특성을 갖는다.  
즉, ```Client```의 상태나 정보를 서버에서 저장하지 않는다는 말이다.  
그럼 ```REST API``` 방식에서는 매번 요청을 하게 되는데,  
어떻게 이 ```Client```가 로그인 한 유저인지 알 수가 있을까?  

그래서 나오게 된 개념이 ```Cookie, Session, Token```의 개념이다.  
사용자의 상태를 저장할 수 있도록 부가적인 요소를 도입하게 된 것이다.  

#### Cookie
쿠키란, ```Client```의 브라우저에 있는 저장소를 의미한다.  
서버에서 특정 값을 생성해서 ```Client```에게 전송했을 때, 쿠키에 저장할 수 있다.  
그러나 이 방식은 굉장히 위험한 방식이다.  

누군가가 쿠키를 탈취하기만 하면, 해당 사용자의 계정으로 로그인하게 되는 것이다.  
굉장히 위험한 방식이기 때문에, ```Session```이 등장하게 된다.  

#### Session
쿠키는 ```Client```측에 정보가 저장되지만, ```Session```은 그렇지 않다.  
서버 측의 ```Session``` 저장소에 저장을 한 뒤, 요청이 오면 검증한다.  

```Client```는, 정보가 아닌 ```Session ID```를 쿠키에 저장하게 된다.  
매 요청시에 해당 ```ID```를 보여 본인의 신원을 입증하게 되는 것이다.  

### Session의 구현
기본적으로 ```Java```에는 ```Servlet```이라는 개념이 존재한다.  
해당 개념을 통해 ```HTTP```간에 오가는 ```Request, Response```를 조작할 수 있다.  
우리는 이제, ```Servlet```과 함께 ```Session```을 조작해보도록 할 것이다.   

기본적으로 ```getSession()```과 ```attribute()``` 동작이 사용된다.  

#### getSession()
아래와 같이 ```getSession()``` 메서드를 사용할 수 있다.  

<img width="673" alt="스크린샷 2024-03-27 오후 11 34 58" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/8c577428-fc0c-48ea-9fab-c90ee4d93428">  

```getSession()``` 메서드는 ```true, false``` 2개의 매개변수를 받는다.  
```true```가 기본값으로, ```Session```이 없는 경우 새로 생성한다.  
```false```인 경우엔 ```Session```이 없는 경우 ```null```을 반환한다.  
아래의 사진과 같이 사용자를 검증할 때 ```false```를 매개변수로 사용할 수 있다.  

<img width="705" alt="스크린샷 2024-03-27 오후 11 38 07" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/aada977b-2c67-4602-8b33-635edda67f5f">  

로그인을 하지 않고 사용하는 경우, 아래와 같이 응답을 받을 수 있다.  

<img width="422" alt="스크린샷 2024-03-27 오후 11 38 55" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/be051164-fa28-4c55-8e3c-78b81621528a">  

로그인을 진행한 뒤에 요청하면, 아래와 같이 정상 응답이 온다.  

<img width="248" alt="스크린샷 2024-03-27 오후 11 39 31" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/5ffde4e7-4abc-4c60-8bc8-98839a4e7f90">   

날아간 ```Request```를 확인해보면 아래와 같은 형태의 요청이 날아갔다.  
```Cookie```에 임의로 생선된 ```JSessionID```가 전송되었음을 볼 수 있다.  
```getSession()```을 하게 되면, 해당 ```ID```를 통해 검증하는 것이다.  

<img width="353" alt="스크린샷 2024-03-27 오후 11 43 24" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/09887335-19c0-4542-8634-a8ac6d805f7c">

#### attribute()
```getAttribute()```와 ```setAttribute()```는 그럼 언제 쓸 수 있을까?  
위의 로그인 로직을 다시 보면, ```setAttribtue()```가 사용되었음을 볼 수 있다.  

<img width="540" alt="스크린샷 2024-03-27 오후 11 42 01" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/6bac32c6-4a85-4dbd-8951-8610727a90d7">  

위와 같이 미리 생성해 놓은 ```Session``` 이름을 사용하여,  
```Session``` 저장소에 해당 이름을 ```Key```로 ```Client```를 저장하는 것이다.  

#### invalidate()
마지막으로 ```invalidate()``` 메서드가 존재한다.  

<img width="463" alt="스크린샷 2024-03-27 오후 11 47 34" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/f7b62833-8616-4523-b953-6370384a42bb">  

아래는 현재 프로젝트에 있는 ```로그아웃``` 로직이다.  
사용자가 ```로그아웃``` 기능을 요청하면, ```Session```을 먼저 받아온다.  
이후, ```invalidate()``` 메서드를 통해 해당 ```Session```을 파기하도록 한다.  
그럼 간단하게 ```로그아웃``` 로직이 끝난다!  

우선은 사용자 검증이 필요한 서비스에만 검증 로직을 구현해 놓았다.  
앞으로는 아래와 같은 일들을 추가적으로 해보려고 한다.  

1. 사용자 검증 로직 리팩토링
2. 세션 저장소에 대한 고민
3. 전체적인 리팩토링
