---
layout: post
title: "DB Auto Increment 오류?"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
프로젝트가 막바지에 갈 수록 DB에 값을 넣는 일이 많아졌다.  
DB를 자주 확인하다 보니 굉장히 이상한 현상이 발생함을 확인했다.  
바로 아래와 같은 현상이다.  
<img width="423" alt="KakaoTalk_20230310_211517230" src="https://user-images.githubusercontent.com/71700079/224524214-17780829-04ea-44c9-b75e-05f0007fd50c.png">  

나는 Primary key에 ```@GenerateValue```를 걸어 놓았다.  
그러면 Key에 ```Auto increment```가 걸려, 생성시마다 값이 증가한다.  
나는 아무런 ```Strategy```도 걸지 않아, 전체 값에 +1이 될텐데  
왜 갑자기 1000이나 값이 증가해버리는 걸까?  
