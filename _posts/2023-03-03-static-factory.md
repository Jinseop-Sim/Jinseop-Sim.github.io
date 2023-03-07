---
layout: post
title: "정적 팩토리 메서드의 적용"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
개발자들 사이에서 가장 공신력 있는 패턴인 __GOF.__  
해당 패턴은 아래와 같이 3가지 패턴으로 구분된다.  
- 생성 패턴(Creative)
  - 객체의 생성과 관련된 패턴이다.
  - ex) Builder, Factory Method, Singleton..
- 구조 패턴(Structural)
  - 클래스나 객체들을 조합해 더 큰 구조로 만드는 패턴이다.
  - ex) Adapter, Bridge, Facade, Proxy..
- 행위 패턴(Behavioral)
  - 객체 간의 상호작용과 관련된 패턴이다.
  - ex) Mediator, Observer, Strategy, Template Method..

그 중 생성 패턴에 해당하는 __Factory Method Pattern__ 를 적용해보려 한다.  

### Factory Method?
Factory Method의 정의는 아래와 같다.  

> 부모 Class에서 객체 생성 Interface를 제공하면,  
> 자식 Class에서 객체의 유형을 변경할 수 있도록 하는 패턴이다.  
> - Refactoring GURU  

쉽게 말하면, 자식 Class에게 Instance의 생성을 위임하는 것!  
즉, 부모 Class는 자식 Class에 대해서 알 필요가 없다!  

예를 들어 내가 게임을 개발한다고 생각해보자.  
처음엔 직업이 ```기사``` 하나 밖에 존재하지 않았다.  
그래서 아래와 같이 ```Knight``` Class를 만들어서 개발했다.  

이제 업데이트를 통해 직업을 늘리려고 한다.  
새 직업 Class를 아래와 같이 만들어 버린다면 어떻게 될까?  
[사진 첨부]

분기문에 따라 행동을 갈라야하고, 그것을 서비스 단에서 처리하니,  
변경사항이 생기면 Class 뿐만 아니라 서비스 로직도 모두 바꿔야 한다.  
이는 곧 __Shotgun Surgery__ 가 발생할 위험이 있다는 말이다.  

이럴 때 사용할 수 있는 것이 __Factory Method__ 패턴이 되겠다.  

### Static Factory Method?
Static Factory Method는 Factory Method와는 조금 차이가 있다.  

#### 참고문헌
[Refactoring GURU : Factory Method](https://refactoring.guru/ko/design-patterns/factory-method)  