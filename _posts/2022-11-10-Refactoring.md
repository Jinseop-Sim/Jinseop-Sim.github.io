---
layout: post
title: "[PNUCSE SE] SOLID?"
categories: SE
tags: [theory]
author:
  - Jinseop Sim
toc: true
math: true
---
> 해당 자료는 부산대학교 채흥석 교수님의 소프트웨어 공학 강의 자료입니다.  

저번 시간에 배웠던 디자인 원칙이나, SOLID 같은 원칙들을 만족하도록  
코드를 수정하는일을 __Refactoring__ 이라고 한다고 마지막에 언급했었다.  

오늘은 Refactoring에 대해서 자세하게 다루겠다.  

### Refactoring
> Any fool can write code that a computer can understand.  
> Good programmers write code that human can understand.  
> - 1999, Martin Fowler, Refactoring  

Code Refactoring은 다음과 같은 절차를 의미한다.  
- Program의 내부 구조를 변경하는 것.
  - 기능 자체를 변경하지는 않는다.
- 내부 속성의 품질을 높이기 위해 사용한다.
  - 즉 유지 보수성의 향상을 위해!
  - 코드의 이해도를 높이고, 변경 용이성을 높이기 위해(OCP)!

### Refactoring : Poor Understandability
![image](https://user-images.githubusercontent.com/71700079/201114346-d3744678-d5a2-4b3f-80b8-803262832843.png)  

모호한 변수의 이름이나, 함수의 이름은 코드 이해도를 떨어트린다.  
타입, 변수의 이름, 함수의 이름을 명확하게 명시해주자!  
![image](https://user-images.githubusercontent.com/71700079/201114380-167cffa1-cc6b-476a-b6f2-54915398390e.png)  

### Refactoring : Introduce Explaining Variable
![image](https://user-images.githubusercontent.com/71700079/201114443-c53880d7-4d94-45c7-8a1c-830f0d9eaa49.png)  

내가 작성한 코드가 어떤 역할인지 명확하게 변수로 표현하라!  

### Refactoring : Extract Method
![image](https://user-images.githubusercontent.com/71700079/201114511-eedf8868-f7cd-4fa9-a651-e72e4bac7eee.png)  

주석 또한, Code Smell 중 하나라고 할 수 있다.  
주석이 많다는 것은 명확하게 이해하기 힘든 Code라는 것!  

공통되는 동작을 하는 문장들을 묶어서 하나의 함수로 추출해낼 것!  

### Refactoring : Move Method
![image](https://user-images.githubusercontent.com/71700079/201114568-776a780b-f98f-4d60-9809-b0693806757a.png)  

관련이 크게 없는 함수를 Class 내에 그냥 두는 것은 응집도를 저하시킨다.  
관련이 있는 Class로 옮긴 뒤에 의존을 함으로써 Method를 이용하자!  

### Refactoring : Split Temporary Variable
![image](https://user-images.githubusercontent.com/71700079/201114621-3fb644ba-38de-4ab2-a41b-9b7cda8cf6ab.png)  

임시 변수로 잠깐 사용할 변수의 이름도, ```temp```로 두고 재사용 하지말 것!  
문장의 의미를 정확하게 표현할 수 있는 변수의 이름으로 정할 것!  

### Refactoring : Introduce Assertion
![image](https://user-images.githubusercontent.com/71700079/201114662-c6e21920-83fd-42ff-8663-b8b142e55094.png)  

실제 Program의 값에 대한 제약에 대해 주석을 적는 것은 옳지 않다.  
차라리 ```Assert()```를 이용해서, 조건 검사를 해주자!  

### Refactoring : Replace Error Code with Exception
![image](https://user-images.githubusercontent.com/71700079/201114765-ce427cfa-7f4a-4018-90dd-9bb44fa5db24.png)  

Error Code를 그냥 반환을 해주지 말고, 명확하게 어떤 예외인지 반환을 하자!  

### Refactoring : Replace Conditional with Guard Clauses
![image](https://user-images.githubusercontent.com/71700079/201114825-84dee395-5711-41ce-a741-f4651b34b519.png)  

다중 if문을 사용하지 말고, __Gaurd Clauses__ 를 써서 바로 ```return``` 하라!  

### Refactoring : Replace Magic Number with Symbolic Constant
![image](https://user-images.githubusercontent.com/71700079/201114867-2aac0e88-8cf9-449d-80ff-92e772fcdaac.png)  

위와 같은 중력 가속도나, 특정 반복 사용 되는 고정 값을 상수로 선언하자.  
상수로 ```define``` 해놓으면 재사용성이 높아지며, 이해도가 높아진다.  

### Refactoring : Replace Parameter with Explicit Method
![image](https://user-images.githubusercontent.com/71700079/201114927-effebfa7-57f0-4bd9-888e-c9464d242292.png)  

위의 함수 ```setValue()```는 연관 없는 두 값을 담아 응집도를 떨어트린다.  

### Refactoring : Split Loop
![image](https://user-images.githubusercontent.com/71700079/201115006-77ef34d9-38d0-4b1c-b56e-33bc9e253df8.png)  

For loop 내에 전혀 연관 없는 두 동작이 들어가 있으면, 응집도를 저하시킨다.  
서로 다른 Loop으로 Refactoring을 해주자!  

### Refactoring : Extract Class
![image](https://user-images.githubusercontent.com/71700079/201115053-f863477f-db53-4d9d-964f-fd772746cad4.png)  

하나의 Class 내에 전혀 연관이 없는 Attribute가 존재함을 알 수 있다.  
그런 경우에는 다른 Class를 만들어 연관이 있는 Attribute 끼리 다시 추출하자!  

### Refactoring : Replace Type Code with Subclasses
![image](https://user-images.githubusercontent.com/71700079/201115106-c7f10b8b-82b6-45a1-af49-572645625616.png)  

위와 같이 Type Code가 존재하는 경우, Code Smell이 난다고 볼 수 있다.  
이런 경우에는 Subclass를 만들고 상속 관계를 만들어 주자!  

### Behavior Preservation
내가 Refactoring을 했을 때, 해당 Code가 그대로 동작하는지 어떻게 확인할까?  
__Test Suite__ 를 이용할 수 있다!  

### For Succesful Refactoring
성공적인 Refactoring은 어떻게 수행할 수 있을까?  

- Refactoring 문화를 장려하자.
  - 누구나 한 번만에 완벽할 수는 없다.
  - 누구나 Refactoring 없이 깔끔한 코드를 짤 수는 없다!
- Test Suite를 항상 충분히 준비해놓자.
  - Test는 Refactoring에서 필수불가결한 요소이다.
  - 항상 System을 구성할 땐 Test를 일상화 하자!
- 조를 짜서 Programming을 하자.
  - 둘이 함께 일하는 것이 따로 일하는 것보다 훨씬 빠르다.
  - Class 작성자와 사용자가 한 조를 이루는 것이 이상적이다!
