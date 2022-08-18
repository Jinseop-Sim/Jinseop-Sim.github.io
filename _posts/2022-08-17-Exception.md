---
layout: post
title: "[SpringBoot] 예외 처리"
categories: Springboot
tags: [java]
author:
  - Jinseop Sim
toc: true
---
웹을 개발하다보면, 수많은 __Exception__ 을 마주하게 된다.  
보통 제공하는 Exception으로 Catch를 하고 Throw를 할 수 있지만,  
필요한 Exception을 Custom해서 사용해야 하는 경우가 있다.  

예를 들어, 로그인을 했는데 해당 사용자가 DB에 존재하지 않는다면?  
또는 해당 사용자는 존재하는데 비밀번호가 틀렸다면?  
이는 DB 단에서 검증을 해야하기 때문에, Exception 처리를 해주어야 한다.  

### Spring에서의 Exception Custom
나는 이번 프로젝트에서 Springboot를 통해 RestAPI 형태로 개발을 했다.  
따라서 Exception 또한 Frontend 단에 JSON의 형태로 에러 코드와 메시지를 보냈다.  

먼저, 아래와 같은 구조로 Class를 구성했다.  
[사진 첨부]  