---
layout: post
title: "[PNUCSE SE] Design Pattern"
categories: SE
tags: [theory]
author:
  - Jinseop Sim
toc: true
math: true
---
> 해당 자료는 부산대학교 채흥석 교수님의 소프트웨어 공학 강의 자료입니다.  

### Typical Design Process
- Top Level Design(Macro-Architectural)
  - Architectural Pattern을 사용하는 단계이다.
  - System 전체 범위에 걸쳐 Design을 한다.
  - Node, Client, Server의 유형이나 수를 설계한다.
  - 각 Node에서 동작하는 Component의 종류와 소통 방식을 결정한다.
  - Global Error을 다루는 정책을 설계한다.
- Component Level Design
  - __Design Pattern__ 을 사용하는 단계이다.
  - Class들 간의 관계를 설계하는 단계이다.
- Detailed Design
  - 각 Class의 내부를 설계하는 단계이다.

위의 설계 절차를 통해서 우리는 Design Pattern이 어디서 쓰이는지 알 수 있다.  
Top Level Design을 지나, Component Level의 설계를 할 때 적용함을 알 수 있다.  
각 Class들 간의 관계를 정의할 때 Design Pattern을 통해 Code Smell을 줄인다!  

- Example : Score Record

### Pros and Cons
- Design Pattern의 장점
  - Inspiration
    - Pattern은 전문가들이 이미 시행착오를 거치며 정의한 것이다.
    - 이를 잘 활용하면 OOP에 적응하는 데에 도움이 많이 된다.
    - 이미 성공한 전문가들이 정의했기에 실패 확률을 낮출 수 있다.
  - Communication
    - 개발자들 간에 간결하고 명확한 의사소통이 가능해진다.
    - OOP에 대한 이해도가 높아져, 소통이 가능해진다.
  - Reusable
    - Software Architecture 측면에서, 재사용성이 매우 높아진다.
- Design Pattern의 단점
  - Pattern을 사용한다고 해서 반드시 재사용성이 높아지는 것은 아니다.
    - Code Reuse가 아닌, Design Reuse이다.
  - Pattern을 과용하는 것은 바람직하지 못하다.
  - Pattern은 자동화가 되어있지 않아서 개발자가 직접 공부하고 구현해야 한다.

### Gang of Four Patterns
- Creational Pattern
  - Class를 바탕으로 어떻게 객체를 만드는 것이 합리적인가?
  - Builder
  - Singleton
- Structural Pattern
  - 큰 규모의 System을 만들 때 어떻게 Class와 객체를 만들 것인가?
  - Adapter
  - Composite
  - Proxy
- Behavioral Pattern
  - Component들 간의 소통, 협업을 어떻게 할 것인가?
  - Template Method
  - Iterator
  - Observer Pattern
  - Strategy Pattern

### Observer Pattern
Observer Pattern의 목적은 Subject의 변경을 Object가 어떻게 쉽게 알아차릴까?에 있다.  
- Subject의 변경을 Object가 Polling하며 일일이 감시하고 있어야 할까?
  - 이는 곧 CPU의 낭비를 의미한다.
- 또한, 모든 Object에 Subject가 직접 Pointer로 연결을 해놓았다면?
  - Object가 새로 추가될 때 마다, Subject Class를 변경해야한다.

위의 방법을 개선하기 위해서 등장한 것이 바로 Observer Pattern이다!  

### Strategy Pattern
Startegy Pattern의 목적은 Context가 Strategy의 상태를 알 수 없게 하는 것!  
해당 목적을 통해 SOLID를 만족시켜 객체 지향적인 Code를 짤 수 있다.  

- Example : ScoreProcessing

### Template Method Pattern
Template Method Pattern의 목적은 중복 Code를 최대한 제거하는 데에 있다.  

- Example : HyundaiMotor
