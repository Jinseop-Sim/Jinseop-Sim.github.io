---
layout: post
title: "[PNUCSE SE] Design Principle Fundamental"
categories: SE
tags: [theory]
author:
  - Jinseop Sim
toc: true
math: true
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
  ![image](https://user-images.githubusercontent.com/71700079/199871554-e076ad1f-13c1-443c-86cd-edaed4faf4ba.png)  

  - Logical Cohesion?
    ![image](https://user-images.githubusercontent.com/71700079/199871522-6b5b4574-71b2-4e09-ae27-00ad978b0408.png)  
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
  - 응집도가 얼마나 부족한지를 나타내는 지표이다. 
  - 이 값이 높을 수록 좋지 않은 코드이다.
  - 값의 계산 식은 아래와 같다.  
    $$if(|P| > |Q|) LCOM = |P| - |Q|$$  
    $$else LCOM = 0$&  
    - ```P```는 공통 참조 Instance가 존재하지 않는 Operation 쌍의 수를 말한다.
    - ```Q```는 공통 참조 Instance가 존재하는 Operation 쌍의 수를 말한다.
  ![image](https://user-images.githubusercontent.com/71700079/199871603-473b6d6d-7178-4b81-86dc-3cbeabfc1377.png)  
    - 위의 그림을 예시로 생각해보자.
    - 좌측 그림에서는 ```P```가 1이며, ```Q```가 2기 때문에 LCOM은 0이다.
      - (op1, op3) | (op2, op3) (op1, op2)
    - 우측 그림에서는 ```P```가 2이며, ```Q```가 1이기 때문에 LCOM은 1이다.
      - (op1, op3) (op2, op3) | (op1, op2)
- LCOM Variant
  - 위의 LCOM1은 최대값을 상정할 수 없다는 문제가 있다.
    - 왜? ```P``` 값이 어디까지 커질 지 한계가 없기 때문이다.
  - 그래서 아래와 같은 새로운 계산식을 도입한다.  
    $$LCOM = 1 - (sum(MF) / M * F) : [0..1]$$  
- LCOM HS(Hendersons-Seller)
  - 위의 계산식의 다른 버전이다.  
    $$LCOM = (M - sum(MF) / F) / (M-1) : [0..2]$$  
  - 위의 두 Variant의 용어들은 아래와 같이 정의한다.
    - M : Class 내의 Method의 총 갯수
    - F : Class 내의 Instance Field의 수
    - MF : Method의 Instance에 대한 참조 횟수
    - 위에서 예시로 들었던 좌측의 그림에서 직접 구해보면 아래와 같다.  
      $$LCOM = 1 - (8/15) = 0.47$$  
      $$LCOM HS = (3 - 8/5) / 2 = 0.7$$  

### Coupling
__Coupling__, 한국말로는 __결합도__ 라고 한다.  

결합도를 평가하는 지표로는 다음과 같은 것들이 사용된다.  
- 과거(1980s)
  - Fan out : 호출되는 Module의 수(Called Method)
  - Fan in : 호출하는 Module의 수(Calling Method)
- Class Coupling
  - RFC(Response For Class)
    - Class 내의 Method 수 + 호출되는 다른 Class의 Method 수
  - CBO(Coupling Between Object)
    - 자신이 참조하는 Class의 수 + 자신이 참조되는 Class의 수
- Package Coupling
  - CE(Efferent Coupling)
    - 자신의 Package에 정의된 Class가 의존하는 다른 Class의 수
  - CA(Afferent Coupling)
    - 자신의 Package에 정의된 Class를 의존하는 다른 Class의 수

### Complexity
__Complexity__, 한국말로는 __복잡도__ 라고 한다.  

복잡도를 평가하는 지표로는 다음과 같은 것들이 사용된다.  
- CC(Cyclomatic Complexity)
  - Code 내 분기문의 수 + 1
  - CC에 따른 결함 발생 위험도
- Nesting Depth
  - 분기문이 몇 중으로 겹쳐있는지를 통해 평가하는 지표이다.
- NPath(Number of static execution Path)
  - 분기문에 의해 몇 가지 경우의 동작이 가능한지 평가하는 지표이다.

어떠한 Code가 위의 원칙들을 지키지 않고 있을 때, 우리는 그것을 바로잡는다.  
그 행위를 바로 __Refactoring__ 이라고 한다!  