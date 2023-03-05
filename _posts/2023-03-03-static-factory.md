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

예를 들어 내가 게임 회사에서 개발을 한다고 생각해보자.  
처음엔 직업이 ```기사``` 하나 밖에 존재하지 않았다.  
그래서 대부분의 코드가 ```기사``` Class에 존재했다.  

그런데 이제 업데이트를 통해 직업을 늘리려고 한다.  
하지만, 대부분의 코드가 ```기사``` Class에 이미 결합 되어있다.  
이대로 새 직업 Class를 만들어 버린다면, 코드는 복잡해 질 것이다.  
분기문에 따라 행동을 갈라야하니, 굉장히 복잡한 코드가 될 것 같다.  

이럴 때 사용할 수 있는 것이 __Factory Method__ 패턴이 되겠다.  

### Static Factory Method?
Static Factory Method는 Factory Method와는 조금 차이가 있다.  

#### 참고문헌
[Refactoring GURU : Factory Method](https://refactoring.guru/ko/design-patterns/factory-method)  