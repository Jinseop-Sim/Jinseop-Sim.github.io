﻿---
layout: post
title: "스프링 JPA - Association"
categories: Springboot
tags: [java]
author:
  - Jinseop Sim
toc: true
---
단순히 Entity를 Table에 맞추어 연관 관계를 짓는다면,  
단순히 외래 키만을 이용해서 조회, 저장을 하므로 객체 지향적으로 접근할 수 없다.  

> 객체는 참조를 사용하며, 테이블은 외래 키를 조인한다.  
> 이것이 객체와 테이블의 가장 큰 차이!  

이로 인해 우리는 협력 관계를 만들 수가 없다!  
그럼 어떻게 해야 우리는 객체 지향적으로 설계를 해서 협력 관계를 만들 수 있을까?  

### 단방향 연관관계
단적인 예시로, 어떤 팀과 그 산하 팀원들이 있다고 생각해보자.  
그럼 아래와 같은 관계를 가질 것이다.  

[사진 첨부]

### 양방향 연관관계
Table은, 관계의 방향이 존재하지 않는다.  
그 말은, 외래키만 만들어 놓으면 양방향으로 모두 JOIN을 통해 접근이 가능하다.  
하지만 객체는 알다시피, 양쪽 모두 Entity가 존재해야 참조가 가능하다.  
이제부터 객체에서 양방향 연관관계를 처리하는 법을 익혀보자.  

#### MappedBy와 Owner(연관관계의 주인)
JPA에서 굉장히 중요한 개념이고, 이해하기 어려운 개념이다.  
객체와 테이블간의 연관관계를 맺는 차이점을 이해하고 있어야 한다!  

- 객체는 2개의 단방향 연결이다.
  - 팀 => 멤버 / 멤버 => 팀
  - 이 말인 즉슨, 객체를 양방향으로 참조하려면 각 Class에 각각 관계를 만들어야한다.
- 테이블은 1개의 양방향(사실상 무방향) 연결이다.
  - 팀 <=> 멤버
  - 외래 키 하나만으로 JOIN을 통해 양쪽 모두 접근이 가능하다.

그럼 객체는 두 클래스 모두 연관 관계 값을 가지고 있는데,  
과연 값을 수정해야 할 때, 어느 것을 외래 키로 관리하고 값을 변경해야 할까?  
그래서 필요한 개념이 바로 __OWNER!__  

- Owner
  - 객체의 두 연관관계 중 하나를 주인으로 지정한다.
  - 연관관계의 주인만이 외래 키를 관리할 수 있다. (등록, 수정)
  - 주인은 mappedBy 속성이 사용되지 않는다.
  - 주인이 아닌 경우엔, mappedBy 속성으로 주인을 지정해주어야 한다.

__그럼 누구를 주인으로 해야할까?__  

외래 키가 있는 곳을 주인으로 정해라!  
테이블 연관관계에서, 외래 키가 있었던 곳을 주인으로 정하라는 의미이다.  
보통, 일대 다 관계에서 항상 외래 키가 존재하는 곳이 "다"의 입장이다.  
이 때, 무조건 "다"의 입장인 곳이 연관관계의 주인이 되는 것이 깔끔하다!  

그리고 반드시 연관관계의 주인에는 값을 입력해 줄 것!  
그리고 순수 객체 상태를 고려해서 항상 양쪽 모두 값을 설정할 것!  

그리고 또 신경써야할 점은,  
- 연관관계 편의 Method를 생성해주자.
  - Class 내에 양방향 모두 관계를 이어주는 Method를 만들어 Atomic하게 관리하자.
- 양방향 매핑시에 무한 루프를 조심하자.
  - toString()이나, Lombok, JSON 생성 라이브러리 등..
  - 무한 루프에 빠질 위험이 존재한다.
  - __JSON을 다룰 때에는, Controller에서 절대 Entity를 반환하지 마라!__

__하지만 실무에서는, 단방향 매핑만으로 일단 연관관계 매핑을 끝낼 것!!__  
양방향 매핑은 그냥 반대방향으로 조회하는 기능이 추가됐을 뿐이다.  
실무에서 JPQL로 역방향 탐색을 할 일이 많다.  
그렇게 필요할 때만 추가를 하면 되는 것이며, 단방향만 잘 짜놓으면 다 된다.  
테이블엔 전혀 영향 없음!   

> - 사진 및 자료 출처 : [김영한의 스프링 JPA 기초](https://www.inflearn.com/course/ORM-JPA-Basic/dashboard)