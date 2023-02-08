---
layout: post
title: "Exception과 Spring Security"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
프로젝트에서 예외 처리를 하던 중, 또 난관에 맞닥뜨렸다.  
다른 Custom Exception은 잘 터지는데, JWT의 Exception 문제가 됐다.  

### Custom Exception
우선 나는 아래와 같이 Custom Exception Handler들을 선언해놓았다.  
<img width="915" alt="KakaoTalk_20230205_165209886" src="https://user-images.githubusercontent.com/71700079/216818470-d9d5de73-4a0d-41fc-a708-6c367376c707.png">  

선언해놓은 Handler가 작동하도록, 아래와 같이 예외를 설정해놓고 터뜨려 보았다.  
<img width="708" alt="KakaoTalk_20230205_164900835" src="https://user-images.githubusercontent.com/71700079/216818489-dfef7ce2-d8c8-4bc1-817a-39129035615a.png">  

- 옳지 않은 ID를 입력해 해당하는 유저가 없을 때
<img width="381" alt="KakaoTalk_20230205_164916895" src="https://user-images.githubusercontent.com/71700079/216818477-f039b23b-7bf0-4a0e-946c-b8300ac87103.png">  

- 옳지 않은 PW를 입력했을 때
<img width="369" alt="KakaoTalk_20230205_165156113" src="https://user-images.githubusercontent.com/71700079/216818482-5df04611-7105-4c23-94db-075e41172eda.png">  

예외를 Front에 JSON 형태로 잘 보내줌을 확인할 수 있다.  

### JWT Exception
하지만 문제는 Spring Security에 관련된 예외를 터뜨릴 때 였다.  
<img width="690" alt="KakaoTalk_20230205_164654145" src="https://user-images.githubusercontent.com/71700079/216818542-5d84219e-8ae3-4bf8-bc3f-4d0253863ab7.png">  

위와 같이 JWT Token이 만료되었을 때 예외를 터뜨리려 했다.  
하지만, 아래와 같은 에러가 발생하며 예상대로 작동하지 않았다.  
<img width="1296" alt="KakaoTalk_20230205_164639988" src="https://user-images.githubusercontent.com/71700079/216818548-2ca0d2a4-2747-43e6-986c-0914ffd5aef2.png">  

그 이유는 다음과 같다고 생각했다.  
- Spring Security는 ```DispatchServlet``` 이전에 ```Filter```로 검증한다.
  - 분명 ```@ControllerAdvice```는 그 이후에 작동을 하는 것 같다.
  - 따라서, ```Filter```에서 미리 Exception이 터져버리는 것이다.
  - 그럼 ```@ControllerAdvice```는 제 기능을 하지 못하게 된다.  

Spring Security Docs에서 말하는 실제 동작은 아래와 같다고 한다.  
![스시](https://user-images.githubusercontent.com/71700079/217487724-b9cfd2ef-4069-4595-8364-d0872c4d19af.png)  

```@ControllerAdvice```는 ```DispatcherServlet``` 까지 내려와  
Runtime에 발생하는 Exception을 잡아내는 기능을 지원한다.  
하지만 그 이전에 Filter에서 Exception이 터져버리므로, 해당 Class엔 아무것도 가지 않는 것이다.  

따라서 Filter를 Custom해서 Front단으로 오류 정보를 넘겨줄 필요가 있다!  

### Solution : Custom Filter
[구현 후 내용 추가 예정]
