---
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

> - 사진 및 자료 출처 : [김영한의 스프링 JPA 기초](https://www.inflearn.com/course/ORM-JPA-Basic/dashboard)