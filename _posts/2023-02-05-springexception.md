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
### ControllerAdvice