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
- Exception은 이미 발생했고, Catch가 되었다.  
  - Throw를 해봤자, 누가 받아서 처리할 것인가?

예외 처리의 원리를 무시한 바보같은 코드였다.  

### 실제 Spring Security의 Exeption 처리
Spring Security Docs에서 말하는 실제 동작은 아래와 같다고 한다.    
![스시](https://user-images.githubusercontent.com/71700079/217487724-b9cfd2ef-4069-4595-8364-d0872c4d19af.png)  

내가 현재 구현하려는 동작은 Throw를 했을 때 Catch 할 누군가가 없다.  
따라서 새로운 Filter를 만들어서 Throw된 Exception을 잡아야 할 것이다!  

아래와 같이 새로운 ```ExceptionFilter```를 구현한다.  
<img width="893" alt="추가예외필터" src="https://user-images.githubusercontent.com/71700079/217564672-5d2c06fc-2bb6-481a-a372-2592a6162cee.png">  

이후 ```addFilterBefore``` Method를 통해 Filter Chain을 구현한다.  
<img width="593" alt="시큐리티 컨피규어" src="https://user-images.githubusercontent.com/71700079/217564767-2de61fdf-e914-4d5c-a8c3-d6b5d677c89b.png">  
<img width="730" alt="시큐리티 필터추가" src="https://user-images.githubusercontent.com/71700079/217564712-d6054a02-62fd-43e0-b9a7-402199c73aa9.png">  

```JWT Filter``` 이전에 ```ExceptionFilter``` 를 Chain 시켰으므로,  
```JWT Filter```에서 Throw하는 Exception을 ```ExceptionFilter```가 Catch한다.  

추가적으로 공식 문서에서는 다음과 같이 Exception 처리를 설명한다.  
![필터쳉니](https://user-images.githubusercontent.com/71700079/217564596-7cb33bf7-ec8a-4dfb-a8bb-c331b781fc81.png)  

그림과 같이 Exception 발생 시, 저 두 부분에서 처리를 하도록 되어있다.  
```ExceptionTranslationFilter```에서 ```doFilter(request, response)```를 호출한다.  
해당 과정에서 어떤 Exception이 발생했는 지에 따라서 결과가 다르게 등장하는데,  
- 미인증(Unauthenticated) : ```AuthenticationEntryPoint``` 가 동작한다.  
- 미인가(Unauthorized) : ```AccessDeniedHandler``` 가 동작한다.  

내가 구현한 Filter에서 미리 Exception을 Catch 후 처리하기 때문에,  
해당 미인증, 미인가 결과 처리 파트 까지는 진입하지 않는다.  