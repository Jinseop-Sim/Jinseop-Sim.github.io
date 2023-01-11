---
layout: post
title: "[Debug] Redis Error와 명령어"
categories: ToyProject
tags: [devops]
author:
  - Jinseop Sim
---
로그인을 위해 Redis를 사용하던 중 굉장히 황당한 Error와 마주했다.  
Redis와 같은 Open Source형 저장소를 처음 써봤기 때문에 그랬는데,  
다시 생각해보면 굉장히 멍청한 행동을 했던 것 같다.  

### Connection Refuse
처음엔 그냥 Redis에 저장하는 코드만 적으면 될거라 생각하고 실행을 했다.  
하지만 돌아오는 건 아래 사진과 같은 오류 메시지 뿐이었다.  

<img width="979" alt="KakaoTalk_20230106_233627601" src="https://user-images.githubusercontent.com/71700079/211851344-16530372-0472-41f9-b346-e54d11b6a32e.png">

곰곰히 생각을 해보니 DB도 접근을 하려면 서버를 켜야하는데,  
오픈 소스 저장소인 Redis 또한 서버를 켜주어야 사용할 수 있지 않을까 싶었다.  
검색을 해보니 아뿔싸, 터미널을 통해 Redis를 키고 사용을 하는 것이 맞았다.  

### Redis 명령어
- Redis 접속 명령어
  - ```redis-server``` : Redis 서버에 접속한다.
  - ```redis-cli``` : Redis 서버의 CLI(Command Line) 환경을 띄운다.
  - ```redis-cli ping``` : Redis 서버가 켜져있는 지 확인 한다.
    - 응답이 ```PONG``` 으로 올 경우 서버가 켜져 있는 것이다.
  - ```redis-cli shutdown``` : Redis 서버를 종료한다.
- Redis 내부 명령어
  - ```Keys *``` : 존재하는 모든 Key를 출력한다.
