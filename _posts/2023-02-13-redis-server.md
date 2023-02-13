---
layout: post
title: "Redis를 배포하려면?"
categories: ToyProject
tags: [devops]
author:
  - Jinseop Sim
---
우리 프로젝트에서는 로그인 기능에 현재 Redis를 사용 중이다.  
__Refresh Token__ 을 Redis에 저장을 하게 되는 방식인데,  
이걸 배포된 EC2 서버에서도 사용하려면 어떻게 해야 할까?  

당연히, EC2 서버에서 Redis를 설치한 뒤 실행을 시켜야 할 것이다.  