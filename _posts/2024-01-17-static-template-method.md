---
layout: post
title: "Static Template Method?"
categories: Tech
tags: [theory]
author:
  - Jinseop Sim
toc: true
---
> __해당 글은 Tstory 블로그 Dev inpa에 출처를 두고 있습니다.__

이번에 알아볼 패턴은 __Static factory method__ 이다.  
흔히 정적 팩토리 메서드라고 불린다.  

### Static Factory Method
이전에 학습했을 때, 팩토리 메서드는 어떤 패턴이었을까?  
객체의 생성에 관련된 패턴이었다.  
객체의 생성을 객체가 아닌 팩토리에 위임하는 것이었다.  

그럼 정적 팩토리 메서드는 어떤 의미일까?  
간단하게 아래와 같이 정의할 수 있을 것 같다.  

> 객체의 생성을 정적 메서드에 위임하자!

저 정의가 정말 이 패턴의 모든 것을 설명 해주었다.  
그래도 아래의 설명에서 좀 더 자세히 알아보자.  

### 사용 이유
