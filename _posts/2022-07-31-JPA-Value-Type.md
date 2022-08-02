---
layout: post
title: "스프링 JPA - 다양한 값 타입"
categories: Springboot
tags: [java]
author:
  - Jinseop Sim
toc: true
---
JPA에서 사용하는 여러가지 Data Type에 대해서 알아보자.  

### Data Type의 분류
- Entity 타입
  - ```@Entity```로 정의하는 객체.
  - Data가 변해도 식별자를 통해서 지속 추적이 가능하다.
  - ex) 회원 Entity의 키나 나이를 바꿔도 식별자를 통해 인식이 가능하다.
- 값 타입
  - ```int, Integer, String``` 처럼 단순한 값으로 사용되는 기본 타입.
  - 식별자가 없고 값만 있으므로, 변경시 추적 불가능.
  - 값 타입은 부작용을 막기 위해 반드시 불변 객체로 만들어야 한다!
    - ex) 객체의 Setter를 없앰. int 대신 Integer, Long을 씀.

### 값 타입의 분류
- 기본 값 타입
  - 기본 값 타입은 생명주기를 Entity에 의존한다.
    - 회원을 삭제하면, 이름 나이 필드도 당연히 삭제된다.
  - 값 타입은 절대 공유하면 안된다! (Side Effect)
    - 특히 기본 타입은 절대로 공유되어서는 안된다!
  - 또한, 값 타입은 절대로 Immutable 해야 한다!
  - ```int, double...``` : 자바 기본 타입
  - ```Integer, Long...``` : Wrapper Class
    - Wrapper은 Class이기 때문에, 공유가 가능하지만 어차피 변경이 되지 않는다!
    - 왜냐하면, Class는 참조만 긁어오는 것이지, 변경이 가능하지는 않기 때문이다.
  - String  

- 임베디드 타입(Embedded Type) : 복합 값 타입
  - 새롭게 내가 정의할 수 있는 타입이다.
  - 주로 기본 값 타입을 모아서 임베디드 타입을 만든다.
  - 임베디드 타입을 정의할 땐 기본 생성자가 필수이다.
    - 임베디드 값 타입의 정의 Class에는 ```@Embeddable```
    - 값을 사용하는 곳에는 ```@Embedded``` Annotation을 각각 달아야 사용 가능하다.
    - 임베디드 값 타입 Class 자체가 NULL이면 내부 Attributes도 모두 NULL이다.
  - ex) 회원 Entity엔 (근무 시작일, 종료일), (도시, 번지, 우편번호)라는 공통 필드를 갖는다.
    - 이 때, 우리는 전자를 workPeroid라는 타입으로, 후자를 homeAddress라는 타입으로 묶을 수 있다.
  - 장점
    - System 전체에서 재사용이 가능하다.
    - Class 내에서는 응집도가 매우 높다.
    - 특정 값 타입만 사용하는 의미있는 메소드를 만들 수 있다.
    - 임베디드 값 타입이 포함된 모든 값 타입은, Entity 생명주기에 의존한다.
    - 객체와 테이블을 아주 세밀하게(Fine-Grained) 매핑하는 것이 가능하다!
- 만약 집 주소, 직장 주소를 모두 넣고 싶다면?
  - 임베디드 타입 속성 재정의가 가능하다.
  - 원래는 임베디드 타입을 중복으로 넣게되면, 오류가 발생한다.
  - ```@AttributeOverrides(@AttributeOverride(name="city", column=@Column(name="WORK_CITY"))```
    - 위와 같은 Annotation을 사용하게 되면, 같은 값 타입을 넣어도 따로 사용이 가능하다!  

- Collection 값 타입 : 자바 Collection (ArrayList, List..)
  - Java Collection(List, Set)을 DB의 특정 Column에 집어 넣는 것은 우선 불가능하다.
  - 따라서 새로운 Table이 생성되어, PK를 통해 매핑을 해주어야 하는 구조이다.
  - ```@ElementCollection``` Annotation을 먼저 적어준 뒤
  - ```@CollectionTable(name = "FAVORITE_FOOD", joinColumns @JoinColumn(name = "MEMBER_ID"))```를 통해 등록한다.
    - 실제로 Member를 영속시켜보면 알 수 있듯이, 새로운 Table이 만들어진다.
  - Collection 값 타입의 변경
    - 하지만, 값 타입의 변경이 문제가 된다.
    - 값 타입은 그 자체를 변경할 수 없어야 하며, 식별자가 없어 추적이 불가능하다고 했다.
    - 따라서 우리가 Collection 내의 Data를 삭제하고 새로 집어넣어주어야 하는데..
    - 이 과정에서 JPA는 Table을 싹 지우고 남은 Data만 다시 Insert하는 과정을 거치게 된다.
  - 따라서 Collection 값 타입은 되도록이면 쓰지 않는다!
    - 대신 Collection 값 타입을 Entity로 한 번 감싸는 식으로 풀어내자!
    - OneToMany를 통해 매핑을 하고, CASCADE와 orphanRemoval을 통해 생명주기 관리를 하도록 하자.

### 값 타입의 비교
- 동일성(Identity) 비교 : 인스턴스의 참조 값을 비교한다. (==)
- 동등성 비교(Equivalence) : 인스턴스의 값 그 자체를 비교한다. (.equals())
  - 내가 생성한 객체의 동등성을 비교하려면, Equals 함수를 Override 해야 한다.
- 값 타입들의 비교는 반드시 동등성을 비교해주자!

> - 사진 및 자료 출처 : [김영한의 스프링 JPA 기초](https://www.inflearn.com/course/ORM-JPA-Basic/dashboard)
