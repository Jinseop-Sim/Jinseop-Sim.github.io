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

내 생각에는 Id 값이 거꾸로 감소하는 것만 아니면 괜찮다고 생각한다.  
Id 값을 Frontend에서 사용할 때에도 내가 보내주는 값만 쓸테니,  
서비스의 동작에도 크게 문제는 없을 것으로 판단된다.  

하지만 정상적인 행동은 아니니 해결 방안을 찾아보았다.  

우선 나는 Maria DB를 사용 중이기 때문에, 해당 공식문서를 읽어보았다.  
공식문서의 ```Missing Value``` 란에 아래와 같이 언급되어 있었다.  

> This happens because if a row is deleted  
> AUTO_INCREMENT value is explicitly updated  
> If the transaction fails the reserved value will be lost.  

다음과 같은 이유로 값이 소실될 수 있다고 한다.  
하지만 나는 위의 이유들에 해당하는 행동을 한 적이 없다.  
다른 이유를 조금 더 찾아보았다.  

### 참고문헌
> [Maria DB docs : Auto increment](https://mariadb.com/kb/en/auto_increment/)  