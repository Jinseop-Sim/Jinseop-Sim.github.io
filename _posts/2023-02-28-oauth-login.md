---
layout: post
title: "Spring Security Bad Credential Exception"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
현재 프로젝트에서 카카오 로그인 API를 사용 중이다.  
이는 소셜 간편 로그인을 이용하기 위함인데,  
그 과정에서 ```BadCredentialException```을 마주하게 됐다.  

해당 예외는 기본적으로 ```AuthenticationException```의 하위 예외이다.  
아래와 같은 상황에 예외가 발생한다.  
- 찾을 수 없는 User일 때
- User의 아이디가 틀렸을 때
- User의 비밀번호가 틀렸을 때

어떤 상황에서 발생한 오류인지는 알려주지 않는다.  
그 이유는 보안을 위해 ```BadCredential```로만 퉁친다고 한다.  
아래와 같이 ```Exception```이 발생함을 확인할 수 있다.  
<img width="1315" alt="badcre" src="https://user-images.githubusercontent.com/71700079/222068717-98df07f3-820f-4381-b0e6-902a059b84e1.png">  

예외의 원인으로 의심되는 상황은 2가지였다.  
1. 회원가입과 로그인을 하나의 Logic에 넣어놓아 문제가 발생했다.
  - 함수는 따로 만들어 놓았기 때문에 가능성은 낮지만 확인해봐야 한다.
  - Transaction 단위의 범위에 대해 생각을 해보아야 할 것 같다.
2. ```loadByUserName()```에서 가져오는 UserDetail에 문제가 있다.
  - ```authentication```은 ```loadByUserName()```을 통해 정보를 가져온다.
  - 나는 해당 함수를 Custom 해놓았기 때문에 확인이 필요할 것 같다.

### 저장이 잘 되었는가?
1번 원인을 조사해보기 위해서, 과정에서 저장이 되는지 확인 해보았다.  
아래와 같이 해당 ```email```로 찾았을 때, ```Member``` 값이 비었는지 확인한다.  
<img width="774" alt="이즈세이브드" src="https://user-images.githubusercontent.com/71700079/222069053-e9adca5b-0c96-427a-a245-54f439b43151.png">  

결과는 아래와 같이 ```false```.  
```isEmpty()``` method를 통해 검증했으니, 이는 저장이 되었다는 의미이다.  
<img width="622" alt="엠프티" src="https://user-images.githubusercontent.com/71700079/222068955-fcee119e-244d-4ce8-b15f-a23914171f5c.png">  

그렇다면 1번 원인은 아님이 확정되었다!  

### UserDetail의 문제
2번 원인을 조사하기 위해서, 일반 로그인 ```Service```를 확인해보았다.  
일반 로그인의 검증 과정에서 아래와 같이 문제를 찾을 수 있었다.  
<img width="792" alt="겟패스워드" src="https://user-images.githubusercontent.com/71700079/222068978-b940c61e-46ee-42a6-bca0-b52b81e086ea.png">  

일반 로그인의 검증에서는 ```loginDto```의 Password를 그대로 넘겨주고 있었다.  
그 말인 즉슨, ```passwordEncoder```에 의해 암호화 되기 전의 상태라는 의미이다.  
하지만 내가 구현한 카카오 로그인 서비스는 아래와 같이 구현해 놓았다.  
<img width="744" alt="카카오 사인업" src="https://user-images.githubusercontent.com/71700079/222082945-719d640d-8ca8-4805-a0ca-c2c28dabdb69.png">  
<img width="1003" alt="KakaoTalk_20230301_020314801" src="https://user-images.githubusercontent.com/71700079/222082974-8b3bbf80-39ae-48f2-bded-cbb98186b137.png">  

암호화해서 저장을 한 뒤, 그대로 암호화 한 Password를 넘겨주었다.  
그러면 당연히 검증을 암호화 된 상태로 진행하니, 예외가 발생할 수 밖에 없는 것이다.  
따라서 아래와 같이 ```kakaoPassword``` 라는 암호화 이전 값으로 바꾸어주었다.  
<img width="742" alt="카카오패스워드" src="https://user-images.githubusercontent.com/71700079/222068994-68d045f2-f439-4eac-a463-e98d11788059.png">  

아래와 같이 카카오 회원으로 회원가입이 잘 되었음을 볼 수 있다.  
<img width="376" alt="멤버" src="https://user-images.githubusercontent.com/71700079/222082888-8400e66a-13dc-422c-b6b3-1f04466c32e0.png">  
