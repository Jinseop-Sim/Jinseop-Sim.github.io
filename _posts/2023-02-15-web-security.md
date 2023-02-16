---
layout: post
title: "Spring Security의 WebSecurity"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
프로젝트를 진행 중에, 서버로 요청을 받는 과정에 문제가 생겼다.  
분명 Test를 위해 임시로 모든 URL을 접근 허가 해놓았는데,  
```favicon.ico```에 의해서 접근이 불가능하다는 오류가 발생했다.  

