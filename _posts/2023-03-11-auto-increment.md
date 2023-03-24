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

> [SQL AUTHORITY.com : Identity Cache](https://blog.sqlauthority.com/2018/01/24/sql-server-identity-jumping-1000-identity_cache/)  
해당 링크는 나와 아예 동일한 문제에 대해 안내하고 있다.  
DB의 ```IDENTITY_CACHE```와 관련된 문제라고 한다.  

SQL-Server DB는 기본적으로 1000까지의 값을 Cache로 만들어놓는 듯 하다.  
DB가 예기치 않은 종료를 맞이했을 때, 미리 만들어 둔 1000의 값이 소실된다고 한다.  
그 여파로 이전 값인 1000을 뛰어 넘어 ```1001, 1002...``` 와 같이 생성된다.  

해당 문제를 해결하기 위해서는 아래의 SQL 명령어를 입력하면 된다고 한다.  
```ALTER DATABASE SCOPED CONFIGURATION SET IDENTITY_CACHE = OFF```  
```IDENTITY_CACHE```를 사용하지 않음으로써, 값의 점프를 막는 것이다.  

Datagrip을 통해서 내가 사용중인 DB에 확인해 보았다.  
하지만 명령어가 먹히지 않았고, 아래와 같은 항목만이 존재했다.  
<img width="603" alt="KakaoTalk_20230321_191405653" src="https://user-images.githubusercontent.com/71700079/226590823-489eab08-47f9-4744-8a4e-a2d783b69a15.png">  

하지만 공식문서에는 다음과 같이 분명 ```CACHE```가 parameter로 명시되어 있다.  
[사진 첨부]  

또한 아래와 같이 실제 DB에도 ```Cache_size```가 1000으로 존재함을 알 수 있다.  
[사진 첨부]  

조금 더 찾아보니, 공식문서에 ```ALTER SEQUENCE```를 통해 설정을 바꿀 수 있다고 되어있다.  

### 참고문헌
> [Maria DB docs : Auto increment](https://mariadb.com/kb/en/auto_increment/)  
> [Maria DB docs : CREATE SEQUENCE](https://mariadb.com/kb/en/create-sequence/)  
> [SQL AUTHORITY.com : Identity Cache](https://blog.sqlauthority.com/2018/01/24/sql-server-identity-jumping-1000-identity_cache/)  
