---
layout: post
title: "[SpringBoot] Spring Security"
categories: Springboot
tags: [java]
author:
  - Jinseop Sim
toc: true
---
### Spring Security
Spring Security는 Spring에서 보안 인증을 제공해주는 스프링 하위 프레임워크이다.  
gradle에 Dependency를 추가함으로써 손쉽게 이용이 가능하다.  
Dependency를 추가하게 되면 아래와 같은 동작이 자동으로 처리된다.  

- 서버가 켜지면 Spring Security 초기화 및 보안 설정이 이루어진다.
- 별도의 설정이가 구현을 하지 않아도 기본적 웹 보안 기능이 시스템에 연동된다.
  - 즉, 모든 요청은 인증이 되어야 자원에 접근이 가능하다.
  - 인증 방식은 Form 로그인 혹은 httpBasic 로그인 방식을 제공한다.
  - 이는 이후 Configuration을 통해 인증 없이 이용할 수 있는 서비스를 제어할 수 있다.

__Filter__ 라는 핵심 요소를 통해서 인증과 인가 처리를 대부분 진행한다.  

#### 인증(Authentication) 관련 Architecture
![spring security](https://user-images.githubusercontent.com/71700079/209428010-d477a81b-81a3-46f0-ae64-f0df28fece58.png)  

#### Spring 주요 Module
