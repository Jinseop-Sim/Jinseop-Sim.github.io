---
layout: post
title: "Spring Security Bad Credential Exception"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
현재 프로젝트에서 카카오 로그인 API를 사용 중이다.  
이는 소셜 간편 로그인을 이용하기 위함인데,  
그 과정에서 ```BadCredentialException```을 마주하게 됐다.  

해당 예외는 기본적으로 ```AuthenticationException```의 하위 예외이다.  
아래와 같은 상황에 예외가 발생한다.  
- 찾을 수 없는 User일 때
- User의 아이디가 틀렸을 때
- User의 비밀번호가 틀렸을 때

어떤 상황에서 발생한 오류인지는 알려주지 않는다.  
그 이유는 보안을 위해 ```BadCredential```로만 퉁친다고 한다.  
아래와 같이 ```Exception```이 발생함을 확인할 수 있다.  
[사진 첨부]

의심되는 상황은 2가지였다.  
1. 회원가입과 로그인을 하나의 Logic에 넣어놓아 문제가 발생했다.
  - 함수는 따로 만들어 놓았기 때문에 가능성은 낮지만 확인해봐야 한다.
  - Transaction 단위의 범위에 대해 생각을 해보아야 할 것 같다.
2. ```loadByUserName()```에 문제가 있다.
  - ```authentication```은 ```loadByUserName()```을 통해 정보를 가져온다.
  - 나는 해당 함수를 Custom 해놓았기 때문에 확인이 필요할 것 같다.

### 저장이 잘 되었는가?

### UserDetail의 문제