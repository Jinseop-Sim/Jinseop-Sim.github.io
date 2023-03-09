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
즉, 부모 Class는 자식 Class에 대해서 알 필요가 없다는 의미이다.  

우선 이 Pattern은 __왜__ 사용되는 걸까?  
근본적으로는 Class간의 결합도를 낮추기 위해 사용한다.  
즉, 다른 Class에 주는 영향을 줄여 유지 보수성을 높임이 목표이다.  
말로는 이해하기 어려우니 예를 들어보자.  

예를 들어 내가 게임을 개발한다고 생각해보자.  
그래서 아래와 같이 3개의 직업 Class를 만들어서 개발했다.  
[사진 첨부]

이제 Service 단에 새 캐릭터를 만드는 기능을 개발하려 한다.  
하지만 지금 상태라면, 아래와 같은 Service 코드가 될 것이다.  
[사진 첨부]

현재는 분기문으로 생성을 갈라야하니, 새 직업이 생길 때마다 Service 코드도 변경해야만 하는 일이 생겨버린다.  

__이는 지금 아래의 원칙을 완벽히 위배하고 있다!__  
- SRP : 변경은 한 가지 이유로만 하도록 한다.
  - Service는 로직을 통해 DB에 저장을 하는 것이 원래 책임이다.
  - 하지만 지금은 객체 생성의 책임까지 지고있다.
- OCP : 변경에는 닫혀있으나, 확장에는 열려있어라.
  - SRP에서 이어지는 부분이다.
  - 새 직업 Class가 생길 때 마다 매번 Service 코드를 변경한다.

이럴 때 사용할 수 있는 것이 __Factory Method__ 패턴이 되겠다.  
아래와 같이 Factory Class를 생성한다.  
![팩토리메서드](https://user-images.githubusercontent.com/71700079/224066721-2b7c5ab2-b761-4ff0-97e8-84d4cc0571ef.png)

이제 생성의 책임은 __Factory Class__ 가 지게 된다.  
새 객체가 생기더라도 Factory만 변경해주면, 알아서 생성을 해준다.  
이는 Service Class의 결합도를 낮춰주며, 최종적으로 개발자 입장에서 유지 보수가 용이하게 하고 가독성 또한 높여준다.  

### Static Factory Method?
Static Factory Method는 Factory Method와는 조금 차이가 있다.  
간단하게 정의하자면, __객체 생성의 역할을 하는 Class Method__ 라고 정의할 수 있겠다.  

가장 간단한 예시로, ```LocalTime``` 객체를 들 수 있다.  
```LocalTime.of()``` Method로 객체를 생성 해본 적이 있을 것이다.  
```LocalTime.of(5, 30)``` 과 같이 시간 객체를 생성할 수 있다.  

또 다른 예시로, ```Enum``` Class의 ```valueOf()```가 있다.  
```Color body = Color.valueOf("Red");```와 같이 객체를 생성한다.  

위의 예시들을 보면 알 수 있듯이, 보통의 생성자와는 다른 형태로 객체를 생성한다.  
그렇다면 해당 구조는 어떤 장점이 있으며, 왜 사용하는가?  

#### 1. 고유한 이름을 가질 수 있다.
보통 생성자는 ```new Product()```와 같이 사용을 하게 된다.  
만약 객체의 생성 목적이 달라 생성자가 2개 이상 존재한다면?  
해당 객체의 생성 목적이 잘 드러나지 않아 헷갈릴 수가 있다.  

이런 문제를 보완하기 위해 정적 팩토리 메서드를 사용하게 된다.  
고유한 Method의 이름을 적어 객체의 생성 목적을 드러내는 것이다.  

예를 들면 아래와 같은 코드가 될 수 있겠다.  
![정적 팩토리 메서드](https://user-images.githubusercontent.com/71700079/224066761-d5aa7d1c-0171-4999-953f-b3b425bc381a.png)

신입사원과 경력사원을 생성하려는 목적이 잘 드러나있다.  

#### 2. 호출할 때마다 새로운 객체를 생성할 필요가 없다.
이 경우는 조금 특이한 경우이다.  
Enum이나 아래의 Day 같이 자주 사용되는 요소가 정해져있다면,  
해당 객체를 미리 생성 해놓고 Cache 처럼 사용이 가능하다.  
새로운 객체를 만드는 것이 아닌, 저장해 놓았던 객체를 꺼내 주는 것!  

[예시 : Day Class]

#### 3. 하위 자료형 객체를 반환할 수 있다.
이 부분에서는 Factory Method와 동일한 역할을 한다.  
사실 이 부분이 Static Factory Method의 기본 역할이라고 생각한다.  
당연히 Factory Method pattern에서 기인했으니, 위와 같이 생각한다.  

[사진 첨부]  

#### 4. 객체의 생성을 캡슐화할 수 있다.
REST API로 개발을 하다보면, 계층 간 Data 전송에 ```DTO```가 사용된다.  
Entity의 정보를 DTO에 담아 보내기도 하고,  
DTO의 정보를 Entity에 보내 새 Entity를 생성하기도 하는 배달부 역할인데,  
그 말인 즉, Entity와 DTO 간의 형 변환이 유연하게 가능해야 한다.  

이럴 때, DTO에 정적 팩토리 메서드를 만들어 둔다면?  
Entity를 넘겨주기만 하면, 해당 메서드가 알아서 변환을 해줄 것이다.  
우리는 아래와 같이 내부 구조를 전혀 알 필요가 없게 되는 것이다.  

아래의 코드 중 어느 것이 가독성이 더 좋을까?  
```EmployeeDto employeeDto = EmployeeDto.from(newEmployee);```  
```EmployeeDto employeeDto = new EmployeeDto(newEmployee.getName(), newEmployee.getAge())```  

이렇듯 객체 지향적으로 개발하고, 가독성을 높여주는 데도 도움이 된다.  

### 결론
앞서나간 개발자들이 닦아놓은 길을 따라감은 매우 중요한 것 같다.  
검증된 기술을 사용하지 않을 이유가 없다고 생각한다.  

하지만 무작정 도입을 하는 것이 아닌, __왜__ 사용하는 지에 대해서 반드시 생각을 하고 적용해야 한다고 생각한다.  

#### 참고문헌
> [Refactoring GURU : Factory Method](https://refactoring.guru/ko/design-patterns/factory-method)  
> [Tecoble : Static Factory Method](https://tecoble.techcourse.co.kr/post/2020-05-26-static-factory-method/)  
