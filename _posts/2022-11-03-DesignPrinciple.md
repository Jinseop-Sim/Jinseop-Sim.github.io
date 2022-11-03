---
layout: post
title: "[PNUCSE SE] Design Principle Fundamental"
categories: HTML
tags: [frontend]
author:
  - Jinseop Sim
toc: true
---
> 해당 자료는 부산대학교 채흥석 교수님의 소프트웨어 공학 강의 자료입니다.  

세상엔 많은 소프트웨어들이 존재하며, 그것들은 매우 방대한 양의 코드로 이루어진다.  
과연 그 코드들을 그냥 규칙 없이 막 짜게 된다면, 신뢰성이 보장이 될까?  

아니다. 가독성도 매우 떨어질 것이며, 분명 많은 결함이 생기게 될 것이다.  
그것들이 큰 문제로 이어지기 전에 우리는 막아야 하며, 예방을 해야 한다!  

그러기 위해서 우리는 지금부터 Design Principle에 대해서 학습을 할 것이다.  

### Design Principle 수립의 목적
위에서 언급한 이유를 통틀어서 __유지, 보수__ 라고 말할 수 있을 것이다.  
우리는 유지보수를 효율적으로, 그리고 더 잘하기 위해서 설계 원칙을 수립하고 준수한다.  

### Cohesion
__Cohesion__, 한국말로는 __응집도__ 라고 한다.  
대상의 내부 요소들이 기능적으로 얼마나 관련이 있는 지를 특정 지표로 나타낸 것이다.  
대상은 아래와 같은 것들이 될 수 있다.  

- Function Cohesion
  - 함수 내의 변수나 문장의 응집도를 의미한다.
  - 아래와 같은 종류의 응집도가 있다.
  [사진 첨부]
  - Logical Cohesion?
    [ 사진첨부 ]
    - 위의 코드를 예시로 생각해보자.
    - if문 분기를 기준으로 둘은 전혀 연관성 없는 서로 다른 동작을 하고 있다.
    - 그럼 굳이 두 동작이 같은 함수 내에 있어야 할까?
    - 전혀 다른 동작이기 때문에, 굳이 그럴 필요가 없고 응집도를 떨어트린다.
- Class Cohesion
  - Class 내의 Attribute나 Operation의 응집도를 의미한다.
- Package Cohesion
  - Packge 내의 Class들의 응집도를 의미한다.
- Component Cohesion

응집도를 평가하는 지표로는 다음과 같은 것들이 사용된다.  

- LCOM(Lack of Cohesion Of Method)
  [사진 첨부]
- LCOM Variant
- LCOM HS(Hendersons-Seller)

### Coupling
__Coupling__, 한국말로는 __결합도__ 라고 한다.  

결합도를 평가하는 지표로는 다음과 같은 것들이 사용된다.  
- 과거(1980s)
  - Fan out : 호출되는 Module의 수(Called Method)
  - Fan in : 호출하는 Module의 수(Calling Method)
- Class Coupling
  - RFC(Response For Class)
  - CBO(Coupling Between Object)
- Package Coupling
  - CE(Efferent Coupling)
  - CA(Afferent Coupling)

### Complexity
__Complexity__, 한국말로는 __복잡도__ 라고 한다.  

복잡도를 평가하는 지표로는 다음과 같은 것들이 사용된다.  
- CC(Cyclomatic Complexity)
  - Code 내 분기문의 수 + 1
- Nesting Depth
  - 분기문이 몇 중으로 겹쳐있는지를 통해 평가하는 지표이다.
- NPath(Number of static execution Path)
  - 분기문에 의해 몇 가지 경우의 동작이 가능한지 평가하는 지표이다.

어떠한 Code가 위의 원칙들을 지키지 않고 있을 때, 우리는 그것을 바로잡는다.  
그 행위를 바로 __Refactoring__ 이라고 한다!  