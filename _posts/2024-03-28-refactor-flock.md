---
layout: post
title: "내 프로젝트는 객체 지향적인가? (4)"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
이번에는 다른 프로젝트를 리팩토링 해보려고 한다.  
이 프로젝트는 기능도 몇 개 없고, 크기가 매우 작기 때문에  
리팩토링을 빠르게 진행하고 기능도 추가해보려고 한다.  

### 세션 리팩토링
이전 게시글에서 세션 기능을 추가했었다.  
하지만 아래와 같이 메서드나 코드의 형태로 서비스 군데군데 흩어져 있었다.  

<img width="617" alt="스크린샷 2024-03-28 오후 8 10 17" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/70f85611-00ff-4348-91da-4228c62eef1f">  

<img width="597" alt="스크린샷 2024-03-28 오후 8 09 38" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/e59d6c32-d21f-4818-8047-242dc74dffb6">

위의 코드들은 분명히 중복되는 코드들이다.  
만약 로직을 수정할 일이 생긴다면, 모두 찾아서 고쳐야 한다.  
너무너무 객체 지향적이지 못한 코드라고 볼 수 있다.  
그럼 어떻게 하나의 클래스로 빼낼 수 있을까?  

아래와 같이 ```SessionUtil``` 클래스를 하나 만들어주었다.  

<img width="657" alt="스크린샷 2024-03-28 오후 8 33 25" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/99abe132-d2ed-4ed4-98e5-e5904bba10ee">  

내부에는 ```getCurrentMember()``` 함수가 존재한다.  
해당 함수를 통해, 세션의 유무를 체크하고 동시에 사용자를 반환한다.  
만약 세션이 없다면, 동일하게 ```Exception```을 응답하도록 한다.  
세션이 있다면? 로그인 된 유저로 판단하고, 멤버의 정보를 그대로 반환하도록 한다.  

잘 작동하는 지 직접 확인해보도록 하자.  

#### 잠깐! 발생한 문제  
잠시 문제가 발생했다.  
위의 함수를 보면 ```Member```를 결과로 반환하기 위해 ```Casting```을 하고있다.  
하지만 그 과정에서 에러가 발생했다.  
```Optional```은 ```Member```로 ```Casting```할 수 없다는 오류를 만났다.  

뭐가 문제일까 잠깐 생각해보니, 꽤 당연하게 발생한 오류였다.  
Member은 내가 직접 만든 객체이니, ```Session```에 어떻게 저장될 지 알 수 없다.  
따라서 안전하게 ```Long```이나 ```String```을 값으로 저장하는게 맞다고 판단했다.  
아래와 같이 함수를 변경해주었다.  

<img width="678" alt="스크린샷 2024-03-28 오후 9 38 46" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/d236f468-85c2-4184-9001-29dfebb1faad">  

<img width="294" alt="스크린샷 2024-03-28 오후 8 24 47" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/f4f2694f-a67f-47b8-9b09-dc0d956199de">  

먼저 회원가입을 진행한 뒤, 로그인을 진행해주도록 하자.  
로그인을 하지 않은 경우, 정상적으로 아래와 같이 응답하게 됨을 볼 수 있다.  

<img width="425" alt="스크린샷 2024-03-28 오후 8 25 52" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/6942c269-fc60-438c-a8f0-678375a3721e">  

