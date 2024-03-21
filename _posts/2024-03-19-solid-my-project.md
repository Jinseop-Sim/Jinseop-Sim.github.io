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

#### 그럼 Controller는?
그 이전에, 잠시 짚고 넘어가야 할 부분이 있다.  
바로 ```Controller```를 추상화 해야 하는가?에 대한 문제이다.  

보통 ```Controller - Service - Repository``` 패턴에서는,  
```Controller```까지 추상화하지는 않는 것으로 알고 있다.  

잠시 생각해보면, ```Controller```는 ```API```가 모여있는 클래스이다.  
그럼 당연히, 용도 별로 모두 경로가 다르게 설정될 것이다.  
따라서 추상화해서 중복되는 코드를 빼낼 필요성이 없는 것이다!  

이제 문제는 ```Page```에 대한 ```Service```를 어떻게 관리할 지이다.  
잠깐 살펴보면 ```Service``` 클래스 내에 중복되는 메서드들이 있음을 볼 수 있다.  

<img width="866" alt="스크린샷 2024-03-20 오후 10 54 49" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/bff35e9a-5983-4349-9906-e1c52984aaf2">  

<img width="906" alt="스크린샷 2024-03-20 오후 10 55 02" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/cc0c0024-4f08-4ecc-84da-7ebae4809607">  

위 사진은 각각 ```GroupBuyingService, SharingService```에 존재하는 두 메서드이다.  
누가봐도 완전히 동일한 기능을 하고 있음을 알 수 있다!  
단, 다른 점이 있다면 의존하고 있는 ```Repository```가 다르다는 점인데,  
이 부분을 해결하기 위해서는 ```Repository```를 먼저 추상화 해야할 것 같다.  

### Repository
```Service```를 추상화하기 이전에, ```Repository```를 먼저 보도록 하자.  
보통 ```Spring Data JPA```를 이용한다면, 기본적으로 ```Repository```가 제공이 된다.  
하지만 내 프로젝트의 경우에는 아래와 같이 직접 메서드들을 ```JPQL```을 통해 구현해 놓았다.  

<img width="726" alt="스크린샷 2024-03-20 오후 11 02 49" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/39346e5d-7f61-4996-ab6f-c8782f2d0b63">  

<img width="715" alt="스크린샷 2024-03-20 오후 11 03 10" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/b2bb65f8-65e6-4f16-8b78-b76e8ad7c856">  

위의 사진을 보면 알 수 있듯, 동일한 기능을 하는 메서드가 존재한다.  
매개변수로 들어오는 게 ```GroupBuying```이냐 ```Sharing```이냐에 따라 다를 뿐이다.  
우리는 앞서 게시글과 관련된 ```Entity```들을 ```Contents```로 추상화 했었다.  
그럼 추상화된 ```Entity```를 여기에 적용 시켜볼 순 없을까?  

<img width="820" alt="스크린샷 2024-03-20 오후 11 29 41" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/52eae018-ba38-4066-9414-8efd61029daa">  

위와 같이 추상 클래스로 ```PostRepository```를 한 번 생성해보았다.  
먼저 기존의 게시글들은 ```TablePerClass``` 전략으로 각자 테이블이 나뉘어 있었다.  
그런 경우엔, 쿼리에 들어가는 테이블 이름이 각자 다르기 때문에 추상화가 불가능하다.  
따라서 먼저 전략을 ```SingleTable``` 전략으로 변경해 주었다.  

<img width="461" alt="스크린샷 2024-03-20 오후 11 33 17" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/8dfe58b3-c3cc-4897-b42b-e1d2349b12d4">  

<img width="242" alt="스크린샷 2024-03-20 오후 11 42 51" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/77e0ac61-70f2-41ac-af46-ad053c374b95">  

전략을 변경한 결과, DB 내의 Table이 잘 변경되었음을 볼 수 있다.  
사실, 실무에서도 ```TablePerClass```는 잘 사용하지 않는다고 한다.  

하지만 안타깝게도, 올바르게 작동하도록 구현할 수 없었다.  
문제는 아래와 같았다.  

#### ```EntityManager```에 대한 주입 관계
<img width="509" alt="스크린샷 2024-03-21 오전 12 05 05" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/fa442698-4f55-4a32-aea5-fac71998028b">  

위의 사진과 같이, ```EntityManager```의 주입에 있어 문제가 발생했다.  
```PostRepository```에도 ```EntityManager```가 주입되어 있고,  
```자식 Repository```에도 ```EntityManager```가 주입되어 있기 때문에,  
아래와 같이 따로 부모의 생성자로부터 ```EntityManager```를 받아와야 한다.  

<img width="511" alt="스크린샷 2024-03-21 오전 12 06 41" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/133be341-2fe1-4f39-9d1c-439e8306e631">  

이런 형태는 깔끔하지 못하다고 생각한다.  

#### ```Downcast``` 불가
<img width="774" alt="스크린샷 2024-03-21 오전 12 08 22" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/19d206eb-5d04-46c7-9b88-9e91f76448eb">  

위의 사진과 같이 ```List<Contents> -> List<GroupBuying>``` 캐스팅은 불가능하다.  
```GroupBuying```는 ```Contents```보다 크기가 큰 ```자식 Class```이기 때문이다.  
```Contents```로 불러와버리는 경우, ```GroupBuying```에 있는 요소들을 매핑할 수 없다.  

위의 문제들로 인해 전략을 수정하기로 했다!  
