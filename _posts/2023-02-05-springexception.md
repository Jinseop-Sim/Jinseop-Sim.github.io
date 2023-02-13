---
layout: post
title: "[SpringBoot] Spring Exception"
categories: Springboot
tags: [java]
author:
  - Jinseop Sim
toc: true
---
항상 모든 Application은 예외처리가 매우 중요하다고 생각한다.  
기능을 잘 구현했다고 하더라도, 예외가 발생하면 쓸 수가 없게 된다.  
따라서 완전한 기능이란, 예외까지 모두 잡은 기능이라고 생각한다.  

Springboot에서는 이런 예외 처리의 용이함을 위해 아래의 기술들을 지원한다.  
1. ```@ExceptionHandler```
2. ```@ControllerAdvice```
3. ```@RestControllerAdvice```

### Exception?
기본적으로, 예외란 두 가지 분류로 나눌 수 있다.
- Checked Exception : 명시적으로 반드시 나타내야하는 예외
  - Runtime Exception을 제외한 모든 Exception
- Unchecked Exception : 명시적으로 나타냄을 강제하지 않는 예외
  - 모든 Runtime Exception
    - ex) ```IllegalArgumentException```, ```NullPointerException```..

### ExceptionHanlder
Spring에서는 각 Controller 내에서 발생하는 Exception을 처리할 수 있도록,  
```@ExceptionHandler``` 이라는 Annotation을 지원하고 있다.  
[사진 추가]  

위 코드와 같이 각 Controller 내에서 발생하는 특정 Exception을 처리해준다.  

### ControllerAdvice
불편하게 모든 Controller에 선언을 해야하는 ```ExceptionHandler```를 보완한,  
전역에 선언해 예외 처리가 가능한, ```@ControllerAdvice``` Annotation이다.  
[사진 추가]

위 코드와 같이 ```@ControllerAdvice``` 라는 Class를 아예 따로 선언한다.  
그럼 전역에서 잡히는 예외를 내부의 ```@ExceptionHandler``` 들이 각각 처리를 하게 된다.  