---
layout: post
title: "Spring Security의 WebSecurity"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
프로젝트를 진행 중에, 서버로 요청을 받는 과정에 문제가 생겼다.  
분명 아래와 같이 Test를 위해 임시로 모든 URL을 접근 허가 해놓았는데,  
```favicon.ico```에 의해서 접근이 불가능하다는 오류가 발생했다.  
<img width="645" alt="SecurityConfig" src="https://user-images.githubusercontent.com/71700079/218955228-6e5e5ae8-1144-4fff-a978-bd2fd185b5a5.png">  

왜인지 이유를 조금 검색해보니 FE의 Static한 Resourec들이 문제였다.  
Static한 리소스, ```favicon.ico```나 ```index.html``` 같은 것들이  
필터를 타게 되어 권한이 없다고 걸러지게 되는 것 같았다.  
따라서 필터에 타지 않게 ```web.ignoring()``` 처리를 해줄 필요가 있다.  

### Web Security
```WebSecurity```를 이용하려면 원래 ```WebSecurityConfigurerAdapter```를 상속받아야 한다.  
하지만 아래와 같이 Spring 공식 문서에 보면 __Deprecated__ 되었음을 알 수 있다.  
<img width="879" alt="Deprecated" src="https://user-images.githubusercontent.com/71700079/218955145-30e90556-c808-4e73-bc3e-74b2cfeb1c1c.png">  

따라서, Spring 공식 문서에서 제공하는 가이드에 따라 아래와 같이 ```@Bean```을 등록한다.  
<img width="998" alt="새 버전" src="https://user-images.githubusercontent.com/71700079/218955261-8994bd94-f156-47df-9a23-efa0e4178b83.png">  
<img width="822" alt="새 버전 적용" src="https://user-images.githubusercontent.com/71700079/218955266-1c67f8c5-6d91-4e99-94f5-d40359e5e499.png">  

```WebSecurityCustomizer```를 등록한 뒤, ```web.ignoring()```을 사용해주면,  
더 이상 Static Resource들이 필터를 타지 않는다.  
공식 문서의 중요성을 다시 한 번 깨닫게 된 에러였다.  

### 추가적인 문제 (2023.02.17 수정)
<img width="1366" alt="웹 시큐리티 오류" src="https://user-images.githubusercontent.com/71700079/220079469-d1290c99-59fb-4084-9832-211dfcca7e0a.png">  

위와 같이 ```ignoring()``` method가 아닌 ```HttpSecurity```를 권장한다고 한다.  
공식 문서에 있어서 사용하긴 했는데, 권장하지 않는 방식인가 보다.  
우선 ```HttpSecurity```에서 ```permitAll()```을 해놓고 좀 더 알아봐야 할 것 같다.  

### 문제 해결 (2023.03.10 수정)
공식문서를 읽는다고 읽었지만, 제대로 읽지 않은 내 불찰이다.  
위의 사진에 보면 공식문서 아래에 __WARNING__ 이 있음이 보인다.  
```authorizeHttpRequest```를 통해 ```permitAll```을 하는 방향을 고려하라고 한다.  

따라서 아래와 같이 코드를 수정했다.  
![image](https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/2d9b200e-2dad-4d64-ab1c-4fba0fdf9db8)  

### 참고문헌
> [Spring docs : WebSecurityConfigurerAdapter](https://docs.spring.io/spring-security/site/docs/5.7.0-M2/api/org/springframework/security/config/annotation/web/configuration/WebSecurityConfigurerAdapter.html)  
> [Spring docs : WebSecurityCustomizer](https://docs.spring.io/spring-security/site/docs/current/api/org/springframework/security/config/annotation/web/configuration/WebSecurityCustomizer.html)  
