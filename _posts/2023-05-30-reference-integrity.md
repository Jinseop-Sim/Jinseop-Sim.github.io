---
layout: post
title: "참조 무결성의 위험성"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
프로젝트의 개발 단계는 마무리 되고,  
자잘한 오류들을 수정하는 디버깅 단계에서 문제가 발생했다.  
회원을 탈퇴시킬 때, __참조 무결성 오류__ 가 발생했다.  

### 참조 무결성 오류?
기본적으로 관계형 데이터베이스는 테이블 간에 관계가 존재한다.  
해당 관계에서 키를 참조하는 테이블이 존재한다면 문제가 발생할 수 있다.  

만약 집과 사람 엔티티가 있다고 가정해보자.  
사람이 만약 그 집에 살고있다면, 사람과 집은 1대1 관계를 가질 수 있다.  
그럼 집 엔티티는 거주인으로 외래키를 가질 수 있다는 말과 같다.  

만약 갑자기 사람이 사라진다면?  
실제로도 퇴거 신청을 하지 않고 나가면 문제가 될 수 있는 것처럼,  
집은 사람을 외래키로 들고 있는데 사람만 삭제되면 당연히 문제가 발생한다.  
이를 우리는 __참조 무결성 위반__ 이라고 부른다.  

### 프로젝트에서의 문제 발생
현재 회원들은 글과 댓글을 작성할 수 있도록 서비스가 제공된다.  
그 말인 즉, 작성한 회원들은 글, 댓글 Entity와 관계를 맺게 된다는 말이다.  
또한 글에 댓글이 작성되기 때문에, 글과 댓글 서로 간에도 관계를 맺고 있다.  

더 나아가, 회원들은 각 글에 좋아요를 누를 수 있게 되어있으며,  
회원들이 직접 참여를 할 수 있는 글의 유형 또한 존재한다.  
위의 두 기능을 구현하기 위해서는 다대다 관계를 교차 엔티티를 통해 풀어야 했으며,  
해당 부분에서도 서로 관계를 맺기에 참조 무결성 오류가 발생할 여지가 있다.  
![qwd drawio](https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/698b5eb3-9602-4ce0-be92-9f8eea20a7b6)  

위의 기능들로 인해, 아래와 같이 탈퇴 시 모든 관계를 제거하는 코드를 작성했다.  
<img width="739" alt="탈퇴 코드" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/a8b540d6-2f46-4c76-bf63-36c5dee9ebb3">  

하지만 그래도 아래와 같이 참조 무결성 오류가 발생함을 확인할 수 있었다.  
<img width="892" alt="댓글 참조 무결성" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/19c9b8af-5c4e-47f7-9ed7-2505eb092b66">  

__왜일까?__

### 해결 방법
이전에 마이페이지를 구현할 때, 동일한 해결 방법을 사용했었던 것으로 기억한다.  
그 때 기록을 해두었음에도 불구하고 잊고 무분별하게 ```CASACADE.ALL``` 을 사용하고 있었다.  
```CASCADE.ALL``` 을 사용하게 되면, ```CASCADE.PERSIST``` 또한 자동으로 실행되는데,  
이 때 관계가 살아있으면, 삭제된 객체가 다시 생성이 되어버리는 경우가 발생한다.  
따라서 ```CASCADE.REMOVE```를 사용함이 옳다고 공식문서를 읽어가며 해결했던 기록이 있다.  

이번에도 아래와 같이 ```CASCADE.REMOVE```로 변경을 해주었고, 탈퇴 로직도 변경을 했다.    
<img width="671" alt="얘도 cascade" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/791d199b-333a-479b-80d4-29644e9f1a71">  
<img width="725" alt="탈퇴코드2" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/767a2c89-09c2-4470-b2d0-85a7d4142f1d">  

곧바로 탈퇴가 정상적으로 진행됨을 확인할 수 있다!  
<img width="233" alt="성공" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/839bcb45-bc8b-46cf-af78-f73cc9497e25">  

__```CASACDE``` 속성을 사용할 때는 조심 또 조심하자!__  