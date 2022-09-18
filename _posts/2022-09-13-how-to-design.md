---
layout: post
title: "Spring Project의 설계 진행 과정"
categories: Springboot
tags: [theory]
author:
  - Jinseop Sim
toc: true
---
Spring으로 Project를 진행하고 설계할 때의 과정을 대략적으로 정리해보자.  

### 요구사항 분석
어떠한 요구가 들어왔을 때 그 요구사항을 제대로 분석함은 매우 중요하다.  
실제 동작하는 화면이나, 기능 목록을 통해서 우리가 구현해야 할 것을 생각한다.  
![요구사항](https://user-images.githubusercontent.com/71700079/190896332-fd793c93-3dce-488e-a3a8-d679b491c870.png)  

### Entity 분석 및 테이블 설계
보통 실무에서는 __DDD(도메인 주도 설계)___ 를 기반으로 SW 설계를 진행한다고 한다.  
![DB 스키마](https://user-images.githubusercontent.com/71700079/190896334-0d298464-7b97-47ac-b207-b2068bcfb9c7.png)  

따라서 우리는 도메인 모델을 정확히 설계하고 그에 맞춰 DB 테이블을 설계해야 한다.  
엔티티에 어떤 값이 들어가야 하는지, Class들이 연관관계를 맺는지 제대로 설계할 필요가 있다.  

### Entity Class 개발
DB 설계를 마쳤으면, 바로 Entity Class 개발에 착수할 수 있다.  

- Entity 설계 시 주의점!
  1. 즉시 로딩은 예측이 어렵고, 어떤 SQL이 실행될지 추적하기가 어렵다.
    - 특히 JPQL을 실행할 때 N+1 문제가 발생활 확률이 높아진다.
    - 따라서 반드시 모든 연관관계는 __LAZY__ 로 설정하도록 하자!
  2. Java Collection은 반드시 필드에서 바로 초기화 해주자.
    - 물론 생성자에서 생성단계에 초기화를 할 수도 있다.
    - 하지만 Best Practice는 필드에서 곧바로 초기화 해주는 것!
    - 이는 NPE로부터 우리를 안전하게 지켜준다.
  3. Table, Column을 생성할 때 이름에 대한 규칙을 적절히 정해야 한다.
    - 보통 Java에선 Camel Case, DB에선 소문자 + Underscore가 관례인 경우가 많다.
    - 스프링은 DB 삽입 시 Camel Case를 자동으로 Underscore로 바꿔준다.
    - 대문자도 소문자로 자동으로 바꿔준다.
    - 물론 실무에 투입되었을 때, 회사에서 정해놓은 내규를 따르는 것이 맞다.
  4. Cascade 설정을 잘 해놓을 것!
    - Cascade 설정을 잘 해놓으면 코드가 매우 간결하고 깔끔해진다.

### Application 개발 : Architecture
이제 실제 애플리케이션을 개발을 할 단계이다.  
우리는 애플리케이션을 개발할 때 계층형 구조를 사용할 것이다.  

- 계층형 구조?
  - Controller(Web) : 실제 웹 API 계층
  - Service : 비즈니스 로직, 트랜잭션 작업을 하는 계층
  - Repository : JPA를 사용하는, DB에 접근을 하는 계층
  - Domain : Entity가 모인 계층
- 개발 순서
  - __Repository -> Service -> Test Code 작성 -> Controller 작성__
  - __이 부분은 추후에 TDD에 관해 포스팅하며 수정하겠다.__

#### DTO의 이용
DTO(Data Transfer Object)란?  
- Client 단과의 소통에 있어서 필요한 값만 뽑아서 만드는 객체이다.  
- DTO는 왜 사용하나요?
  - Entity의 모든 속성이 외부에 노출되면 보안 문제가 발생할 수 있다.
  - 위에서 말했다시피, 필요한 값만 뽑아서 객체를 만들기 때문에 효율적이다.
  - 계층적 구조에 있어서, 계층 간의 분리를 확실하게 할 수 있다.

### Application 개발 : Repository
DB에 실질적으로 접근을 하는 계층인 Repository 계층이다.  
Spring JPA를 이용하기 위해서, 반드시 ```EntityManager``` 를 등록해야 한다.  

{% highlight java %}
@PersistContext // 표준 Annotation, Springboot는 @Autowired 가능.
private EntityManger em;
{% endhighlight %}

### Application 개발 : Service
실제 Repository를 주입 받아서 기능을 사용하고, 로직이 들어가는 부분이다.  
Repository의 기능을 이용해 DB에 변경을 발생시키기 때문에 Transaction 단위로 설계한다.  

들어온 값에 대한 __Validation__ 또한 이 Service 단에서 검증한다.  
Validation 또한 비즈니스 로직이므로, Service 단에 설계를 하는 것이 옳다.  

기본적으로 코드에 ```@Service``` Annotation을 넣어 Spring Bean으로 등록하도록 한다.  
생성자 주입 방식으로 Repository를 주입받아서 사용한다.  

### Application 개발 : Controller

### Application 개발 : View
실제로 Client 단에 뿌려지는 View를 개발하는 방식에는 두 가지가 존재한다.  

- SSR(Server Side Rendering)
  - 서버에서 직접 HTML을 들고, Data를 HTML로 뿌려주어 Rendering 하는 방식.
  - JSP, Thymeleaf 등 사용.
- CSR(Client Side Rendering)
  - 서버에서 HTML을 들고있지 않는다.
  - 서버에서 Client(Frontend) 단으로 Data를 특정 형태로 보내준다.
  - 그럼 Client 단에서 해당 Data를 가공해서 Client에게 Rendering 해주는 방식.
  - React, Vue 등을 통해 Javascript Coding.
