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

### Sequence의 IDENTITY_CACHE
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
![w](https://user-images.githubusercontent.com/71700079/227532252-6504f946-f003-490a-a5df-b86bab7c8d0e.png)  

또한 아래와 같이 실제 DB에도 ```Cache_size```가 1000으로 존재함을 알 수 있다.  
 <img width="942" alt="ㅂㅈ" src="https://user-images.githubusercontent.com/71700079/227534540-791ac29b-3a10-4584-abd8-69304cc302e8.png">  

조금 더 찾아보니, 공식문서에 ```ALTER SEQUENCE```를 통해 설정을 바꿀 수 있다고 되어 있었다.  
![image](https://user-images.githubusercontent.com/71700079/227535333-b4a2f227-c2e8-419a-a5ce-7c224828ab0b.png)  

따라서 다음과 같이 ```DataGrip```에서 ```NOCACHE``` 옵션을 통해 수정했다.  
<img width="464" alt="KakaoTalk_20230324_223339378" src="https://user-images.githubusercontent.com/71700079/227535524-a9fb8658-788f-4350-9cfb-c26222dd2411.png">  
다시 확인을 해보면 ```Cache size```가 0으로 바뀌었음을 확인할 수 있다.
<img width="677" alt="KakaoTalk_20230324_223403807" src="https://user-images.githubusercontent.com/71700079/227535474-1416abd9-7779-409b-8849-a87f5db9e700.png">  

### 실제 적용
실제로 잘 동작하는지 기간을 두고 지켜보았다.  
원래는 아래와 같이 1000씩 건너뛰어 이상한 증가세를 보였다.  
<img width="366" alt="KakaoTalk_20230327_191700146" src="https://user-images.githubusercontent.com/71700079/228291468-725bed01-7aa9-48aa-8c67-34e22a79c014.png">  
하지만 3월 26일 이후로는 7000번대로만 ```id```가 생성됨을 볼 수 있다.  
<img width="837" alt="KakaoTalk_20230327_191214078" src="https://user-images.githubusercontent.com/71700079/228291477-bc8108d2-d560-44dd-ba84-e11fe2645216.png">  
<img width="847" alt="KakaoTalk_20230327_191305085" src="https://user-images.githubusercontent.com/71700079/228291484-6f4e8ed9-a62e-477f-8a15-9d8916aa11a4.png">  

### 참고문헌
> [Maria DB docs : Auto increment](https://mariadb.com/kb/en/auto_increment/)  
> [Maria DB docs : CREATE SEQUENCE](https://mariadb.com/kb/en/create-sequence/)  
> [SQL AUTHORITY.com : Identity Cache](https://blog.sqlauthority.com/2018/01/24/sql-server-identity-jumping-1000-identity_cache/)  