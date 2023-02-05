---
layout: post
title: "Exception과 Spring Security"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
프로젝트에서 예외 처리를 하던 중, 또 난관에 맞닥뜨렸다.  
다른 Custom Exception은 잘 터지는데, JWT의 Exception 문제가 됐다.  

### Custom Exception
우선 나는 아래와 같이 Custom Exception Handler들을 선언해놓았다.  
[사진 첨부]

선언해놓은 Handler가 작동하도록, 예외를 설정해놓고 터뜨려 보았다.  
[사진 첨부]

- 옳지 않은 ID를 입력해 해당하는 유저가 없을 때
[사진 첨부]
- 옳지 않은 PW를 입력했을 때
[사진 첨부]

예외를 Front에 JSON 형태로 잘 보내줌을 확인할 수 있다.  

### JWT Exception
하지만 문제는 Spring Security에 관련된 예외를 터뜨릴 때 였다.  
[사진 첨부]  

위와 같이 JWT Token이 만료되었을 때 예외를 터뜨리려 했다.  
하지만, 아래와 같은 에러가 발생하며 예상대로 작동하지 않았다.  
[사진 첨부]  