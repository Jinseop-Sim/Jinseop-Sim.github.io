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

### 왜 EC2 서버에서 띄워야 하는가?
내가 테스트를 할 때에는 ```localhost``` 환경에서 사용을 했다.  
그럼 ```Loopback IP```로 요청을 보내고 응답을 받는다는 말이다.  
Redis 또한 하나의 Process 이므로, Port(6397)만 다를 뿐  
내가 사용 중인 ```Loopback IP```로 요청이 들어와야 사용이 가능하다.  

따라서, 외부의 요청은 내 ```localhost IP```로 들어올 수가 없으니,  
EC2 서버의 IP로 요청을 받을 수 있도록 Redis를 그곳에 띄워야 한다.  

어디까지나 얕은 네트워크 지식을 통한 내 생각이다.  

### Redis Install