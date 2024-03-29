---
layout: post
title: "Static Template Method?"
categories: Tech
tags: [theory]
author:
  - Jinseop Sim
toc: true
---
> __해당 글은 Tstory 블로그 Dev inpa에 출처를 두고 있습니다.__

이번에 알아볼 패턴은 __Static factory method__ 이다.  
흔히 정적 팩토리 메서드라고 불린다.  

### Static Factory Method
이전에 학습했을 때, 팩토리 메서드는 어떤 패턴이었을까?  
객체의 생성에 관련된 패턴이었다.  
객체의 생성을 객체가 아닌 팩토리에 위임하는 것이었다.  

그럼 정적 팩토리 메서드는 어떤 의미일까?  
간단하게 아래와 같이 정의할 수 있을 것 같다.  

> 객체의 생성을 정적 메서드에 위임하자!

저 정의가 정말 이 패턴의 모든 것을 설명 해주었다.  
그래도 아래의 설명에서 좀 더 자세히 알아보자.  

### 사용 이유
개발을 하다 보면 생성자를 여러개 만들어야 하는 경우가 생긴다.  
그럼 매개변수만으로 생성자를 구분해야 하는 경우가 생기는데,  
앞서 포스팅했던 빌더 패턴을 통해 생성자를 줄일 수가 있겠다.  

하지만 빌더는 생성자보다 비용이 크기 때문에 Trade-off를 고려해야 한다.  
그럴 때 우리는 새로운 선택지로 정적 팩토리 메서드를 사용할 수 있다.  

### 구현
아래의 예시를 한 번 보도록 하자.  
잠시 기업의 이름을 살짝 빌려, 카카오와 네이버 유저를 만든다고 해보자.  
정적 팩토리 메서드를 사용하여 아래와 같이 메서드를 구현할 수 있다.  
하나의 생성자로 두 개의 다른 방식의 유저를 생성함을 볼 수 있다.  

<img width="768" alt="스크린샷 2024-01-18 오전 2 16 39" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/27b12d7a-8a85-4190-9491-effd313cef90">  

해당 메서드들은 실제 객체 생성 시 아래와 같이 사용할 수 있다.  

<img width="955" alt="스크린샷 2024-01-18 오전 2 18 27" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/2cf5d0e8-7121-4373-8bed-5ca25de93435">  

### 실제 사용
이전에 토이 프로젝트로 진행했던 코드에 적용할 수 있을 것 같다.  
아래의 사진에 보면 댓글 객체에 두 개의 생성자가 있음을 볼 수 있다.  

![image](https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/868934a2-2ef4-4343-ab0f-051cef231ea3)  

댓글과 답글 기능을 구현하기 위해 계층 구조로 구성이 되어있다.  
따라서 댓글을 달 때의 생성자와 답글을 달 때의 생성자가 다른 것인데,  
답글을 달 때는 매개변수로 ```parent```를 받고 있음을 확인할 수 있다.  

이럴 때 우리는 생성자를 하나로 줄일 수 있다!  
아래와 같이 코드를 수정하면 될 것이다.  

<img width="921" alt="스크린샷 2024-01-18 오후 3 48 29" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/725b17fe-4447-4849-be6c-e75620b9ba1d">  

생성자 하나를 두고 두 개의 정적 팩토리 메서드를 구현했다.  
이렇게 되면 이름을 통해 객체 생성의 목적이 확실해 졌다.  
또한 생성자를 여러개 두지 않아도 되어 코드가 보다 간결해졌다.  

<img width="836" alt="스크린샷 2024-01-18 오후 3 49 38" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/92b0451e-f6e8-42e8-ad76-2b244d1faa78">  

실제로 사용되는 부분에서는 위와 같이 사용할 수 있다.  
```Comment.commentWithoutParent()```는 첫 댓글,  
```Comment.commentWithParent()```는 답글 서비스에 들어가있다.  
이렇게 실제 서비스의 리팩토링에도 사용할 수 있는 패턴이다.  
