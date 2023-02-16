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
Redis를 설치해서 이용하는 방식은 크게 두 가지가 있다.  

1. Redis Server를 EC2에 설치해서 사용
2. ElastiCache(외부 서버)를 통해 사용

후자는 EC2에서 제공하는 ElastiCache라는 서비스인데,  
Free tier지만 혹시 사용하다가 과금이 될까봐 전자를 사용하기로 했다.  
신기술은 다음번에 사용해보기로 하자..  

SSH를 연결을 통해 Termianl에서 EC2에 접속한 뒤  
```
sudo apt-get update
sudo apt-get install redis-server
```
위의 명령어를 통해서 Redis server를 EC2에 설치해주자.  
그리고 Redis 사용을 위해서 설정을 조금 해주어야 한다.  

```
sudo vi /etc/redis/redis.conf
```
위의 명령어를 통해 기본 Configuration을 수정해야 한다.  
__접속 가능한 IP, Max Memory, Memory 교체 알고리즘__ 을 수정한다.  
