---
layout: post
title: "스프링 MVC - 기본 기능"
categories: Springboot
tags: [java]
author:
  - Jinseop Sim
---

본격적으로 Spring MVC에 대해 공부를 시작했다.  
가장 먼저, Spring MVC의 기본 기능들에 대해서 알아보자.  

### Controller? RestController?

### Bonus : Logging
우리는 보통 Debugging을 할 때, ```System.out.println```을 통해서 했었다.  
하지만 실무에서는 ```Logger```를 이용해서 로그를 찍는다!  

### HTTP Message Converter
[사진 첨부]

기본적으로, Controller들의 Return 값은 View name이 된다.  
View Resolver가 반환하는 View name을 보고 띄우고자 하는 HTML을 찾아 띄워준다.  
하지만, ```@ResponseBody```가 존재할 때는 Converter가 작동해서 응답에 반환 값을 넣어버린다!  

아래와 같은 경우에는 Message Converter가 작동하게 된다.  

- 요청 : ```@RequestBody```, ```HttpEntity(RequestEntity)```
- 응답 : ```@ResponseBody```, ```HttpEntity(ResponseEntity)```
  - __Class Type__ 과 __Media Type__ 을 체크해서 Converter를 결정한다.
  - Class Type : ```String, byte[], HashMap```..
  - Media Type : ```application/json, text/plain```..
  - Class Type이 맞지 않거나 Media Type이 다르면 작동을 멈춰 버린다.

### Request Mapping Handler
[사진 첨부]

Annotation 기반의 Controller들은 매우 다양한 Parameter을 사용할 수 있었다.  
도대체 어떻게 이렇게 Type에 따라 유연한 처리가 가능할까?  
바로 __Argument Resolver__ 가 존재하기 때문이다.  

Handler Adapter가 Argument Resolver를 호출하고, 필요한 Parameter의 준비가 끝나면  
그제서야 Handler Adapter가 Handler(Controller)을 호출을 하게 된다.  

이후 Return Value Handler가 작동을 하는데, 응답 값을 변환하고 처리한다.  
예를 들면, ```ModelAndView, @ResponseBody, HttpEntity``` 등이 있다.  
이 친구 덕분에 String으로 View Name을 반환해도 View가 Resolve 되는 것이다!  

결국 위의 동작을 살펴보면, HTTP Message Converter은 어느 단계에 존재할까?  
Argument Resolver, Return Value Handler 두 개에 개입함을 알 수 있다.  