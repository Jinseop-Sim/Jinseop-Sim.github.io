---
layout: post
title: "스프링 MVC - Thymeleaf"
categories: Springboot
tags: [java]
author:
  - Jinseop Sim
toc: true
---

우리는 MVC Pattern에 대해서 공부할 때, 요청에 대한 응답을  
반환할 수 있는 3가지 방법에 대해서 배웠다.  

1. Static Resource
2. View Template (Dynamic View)
3. Message Body

그 중 동적 View를 반환하기 위해서 우리는 JSP를 사용했었는데,  
이제 더욱더 편리한 Thymeleaf Engine에 대해서 배워보자.  

### Thymeleaf Declare
```<html xmlns:th="http://www.thymeleaf.org">``` 로 선언하여 사용한다.  

### Thymeleaf의 핵심
```th:xxx``` Tag가 붙어있는 코드는 Server단에서 렌더링이 된다.  
하지만 HTML File을 열면, ```th:xxx``` Tag가 없는 코드로 보이게 된다.  

### Thymeleaf Link
```th:href = "@{/css/bootstrap.min.css}"```  

URL Link를 걸어서 쓰고 싶은 경우, ```@{...}``` 을 사용한다.  

### Thymeleaf Attribute
```<td><a href="item.html" th:href="@{/basic/item/{itemId}(itemId=${item.id})}"```  
위 코드는, HTML을 그냥 열면 ```href```로 이동하지만,  
Server단에서 렌더링을 하는 경우엔, ```th:href```가 열리게 된다.  
이를 타임리프에서 __속성 변경__ 이라고 한다.  

> - 사진 및 자료 출처 : [김영한의 스프링 MVC 1편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1/dashboard)