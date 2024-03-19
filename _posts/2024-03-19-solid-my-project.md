---
layout: post
title: "내 프로젝트는 객체 지향적인가?"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
과연 내가 프로젝트 동안 작성했던 코드는 객체 지향적일까?  
프로젝트 코드를 회고하며, __Refactoring__ 을 최대한 해보려 한다.  

### 객체 지향?
먼저 객체 지향적이란 어떤 것일까?  
```추상화, 다형성, 상속, ..``` 다양한 말로 설명할 수 있을 것이다.  
하지만 나는 간단하게 아래와 같이 생각한다.  

> __코드의 가독성을 높이고, 유지보수성을 높이자!__

사실 위의 말이 객체 지향의 철학, 그 자체이다.  
복잡한 문제를 좀 더 관리하기 쉽고 읽기 쉽게 만드는 것.  
코드를 모듈화하고 캡슐화해서 체계적이고 유지, 관리하기 쉽도록 하는 것!  

과연 내 코드들은 이런 부분에서 완벽할까?  

### Entity
먼저, DB에 테이블로 저장될 Entity부터 살펴보도록 하자.  
아래의 사진과 같이 용도 별로, 패키지와 클래스를 구분해 놓았다.  

<img width="304" alt="스크린샷 2024-03-19 오후 11 05 30" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/dad9a282-ede7-4136-ae1e-4fa4ae6133d3">

여기서 게시글이 모여있는 ```Page``` 패키지를 주목해보자.  
아래의 사진과 같이 ```Content``` 클래스를 모두 상속하고 있다.  

<img width="349" alt="스크린샷 2024-03-19 오후 11 07 00" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/075c3bb0-5518-4d27-9809-1e6cbbc6e06b">  

아래의 사진은 ```Content``` 클래스의 내부 모습이다.  
중복 코드를 줄이기 위해 추상 클래스로 선언하였다.  
각 페이지들에서 중복되는 부분을 가져다 클래스로 모아놓은 것이다.  
필드들은 외부에서 접근하지 못하고, 상속 받은 클래스만 접근하도록 ```protected```로 선언하였다.  

<img width="515" alt="스크린샷 2024-03-19 오후 11 14 34" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/eb8e7dfe-998a-4638-9d2d-d2d573459d1c">

하지만 살펴보니, ```카테고리, 설명``` 필드도 중복되는 요소였다.  
또한, 핵심적으로 아래와 같이 생성자에 중복되는 요소들이 발견되었다.  

<img width="589" alt="스크린샷 2024-03-19 오후 11 57 11" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/ed7a19ff-eac9-4c51-b3a6-fe8c5a6e4743">  

상위 4개의 필드가 현재 중복되어 상속받은 요소들이다.  
이렇게 코드를 작성하면, ```상속 및 추상화```를 적용한 의미가 없어진다.  
아래와 같이 코드를 수정해보았다.  

<img width="924" alt="스크린샷 2024-03-20 오전 12 10 10" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/b9760fd1-903c-471d-8727-f33a893fb507">  

<img width="647" alt="스크린샷 2024-03-20 오전 12 10 21" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/1903a351-50d8-4500-8013-af0e9882da8c">

위와 같이 부모 클래스에 공통 요소 생성자를 구현한다.  
```super()``` 메서드를 이용해 부모 클래스의 생성자를 이용하고,  
자식 클래스에 개별적으로 있는 필드들 또한 초기화 해주도록 한다.  
이렇게 구현해야 깔끔한 객체지향이라고 생각한다!  

아래와 같이 간이로 글을 생성하는 테스트를 진행해 보았다.  

<img width="478" alt="스크린샷 2024-03-20 오전 12 59 11" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/e8ece41e-77cf-4aa3-a06b-630bd754fd96">  

<img width="957" alt="스크린샷 2024-03-20 오전 12 59 54" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/23f64580-a2a5-4aee-ae7c-6e4c820ffa20">  

성공적으로 생성됨을 확인할 수 있다!  

### Service
그럼 이번엔 비즈니스 로직이 구현된 ```Service```로 넘어가보도록 하자.  
과연 ```Service``` 패키지에는 객체 지향적으로 코드가 잘 짜여있을까?  

### Repository
마지막으로, 실제 DB에 접근하는 ```Repository```이다.  
과연 ```Repository``` 패키지는 객체 지향적으로 잘 구현되었을까?  

