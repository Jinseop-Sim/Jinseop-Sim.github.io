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
<img width="645" alt="SecurityConfig" src="https://user-images.githubusercontent.com/71700079/218955228-6e5e5ae8-1144-4fff-a978-bd2fd185b5a5.png"> 
<img width="879" alt="Deprecated" src="https://user-images.githubusercontent.com/71700079/218955145-30e90556-c808-4e73-bc3e-74b2cfeb1c1c.png">  
<img width="998" alt="새 버전" src="https://user-images.githubusercontent.com/71700079/218955261-8994bd94-f156-47df-9a23-efa0e4178b83.png">  
<img width="822" alt="새 버전 적용" src="https://user-images.githubusercontent.com/71700079/218955266-1c67f8c5-6d91-4e99-94f5-d40359e5e499.png">  
