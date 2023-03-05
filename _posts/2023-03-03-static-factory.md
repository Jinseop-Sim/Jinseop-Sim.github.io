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
Factory Method의 정의는