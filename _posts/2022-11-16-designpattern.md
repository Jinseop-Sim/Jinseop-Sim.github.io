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
Subject Interface, Object Interface를 각각 만들고 Method를 정의한다.  
이후 Subject Class와 Object Class들은 Interface를 구현하기만 하면 된다.  

### Strategy Pattern
Startegy Pattern의 목적은 Context가 Strategy의 상태를 알 수 없게 하는 것!  
해당 목적을 통해 SOLID를 만족시켜 객체 지향적인 Code를 짤 수 있다.  

- Example : ScoreProcessing  
<img width="528" alt="스크린샷 2022-11-21 오전 11 15 47" src="https://user-images.githubusercontent.com/71700079/202946382-4c9b30c8-3aea-49a4-80ee-a97c2eb8ec81.png">  

위의 코드는 Pattern을 적용시키기 이전의 코드이다.  
하나의 Module에 3개의 기능이 모두 들어간 ```analyze()``` 함수는 굉장히 응집도가 떨어져 보인다.  
따라서 Refactoring이 필요하다. 아래와 같이 응집도를 높여보자.  

<img width="651" alt="스크린샷 2022-11-21 오전 11 21 45" src="https://user-images.githubusercontent.com/71700079/202947659-63b9b4fe-5c60-4583-8988-d59cbdd1368c.png">  

하지만 여전히 문제는 남아있다. 새로운 계산식을 적용하려면 함수를 수정해야 한다는 것이다.  
이는 OCP(개방 폐쇄 원칙)에 위배되는 Code이다. 따라서 다음과 같은 Pattern을 적용시킨다!  

<img width="890" alt="스크린샷 2022-11-21 오전 11 23 04" src="https://user-images.githubusercontent.com/71700079/202948222-316189c8-e268-42fe-8b53-d57b6a93f87d.png">  

결국 Context는 해당 Strategy Interface에만 의존하면 된다(DIP).  
새로운 기능을 사용하고 싶다면, 새로운 Concrete Strategy Class를 구현해 갈아끼우기만 하면 되는 훌륭한 Pattern이다(OCP)!  

### Template Method Pattern
Template Method Pattern의 목적은 중복 Code를 최대한 제거하는 데에 있다.  

- Example : HyundaiMotor
<img width="703" alt="스크린샷 2022-11-21 오전 11 46 59" src="https://user-images.githubusercontent.com/71700079/202952548-6a9aa9c0-2988-49f4-b0d9-45023a20b1ea.png">  

위와 같은 Code를 HyundaiMotors에서 만들어 놓았다고 가정하자.  
이 때, LGMotors에서도 모터를 만들기 위해 해당 Code를 참고해 아래와 같은 Code를 짰다.  

<img width="682" alt="스크린샷 2022-11-21 오전 11 49 06" src="https://user-images.githubusercontent.com/71700079/202952745-b2fe9519-e6d5-4541-b0af-89cded5cb7c1.png">  

하지만 두 Code는 너무 중복되는 Code가 많으며, 이름만 다르지 거의 같은 Code라고 볼 수 있다.  
이런 경우에는, 하나를 바꾸면 다른 Class도 바꾸어야하는 __Shotgun Surgery__ 의 위험이 있다.  
따라서 아래와 같이 Abstract Class의 상속 관계를 이용해서 해결하자.  

<img width="1009" alt="스크린샷 2022-11-21 오후 3 01 00" src="https://user-images.githubusercontent.com/71700079/202976725-1c4a415a-98c7-4dc6-85f4-2ceb4e061805.png">  

위와 같이 Class의 상속 관계를 만들어서 처리하는 경우, ```move()``` 함수를 __Template Method__  
또, ```moveMotor(direction)``` 함수를 __Primitive Operation__ 이라고 칭한다.  
위의 Class를 실제 Java Code로 바꾸면 아래와 같이 표현할 수 있다.  

<img width="917" alt="스크린샷 2022-11-21 오후 3 05 30" src="https://user-images.githubusercontent.com/71700079/202977184-c977dc35-a1ff-4540-9d4e-59e9743f79a2.png">  

```move()``` 함수를 공통으로 묶었지만, ```moveMotor()``` 함수는 두 Class가 다르게 동작을 했었다.  
따라서 ```move()``` 함수 사용 시, 참조한 Class Type에 따라 ```moveMotor()``` 함수가 다르게 동작하도록   
Overriding을 통해 설계를 한다. 어차피 Motor Class 밖에선 쓰지 않으니 Protected로 선언한다.