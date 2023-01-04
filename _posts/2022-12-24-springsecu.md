---
layout: post
title: "[SpringBoot] Spring Security"
categories: Springboot
tags: [java]
author:
  - Jinseop Sim
toc: true
---
### Spring Security
Spring Security는 Spring에서 보안 인증을 제공해주는 스프링 하위 프레임워크이다.  
gradle에 Dependency를 추가함으로써 손쉽게 이용이 가능하다.  
Dependency를 추가하게 되면 아래와 같은 동작이 자동으로 처리된다.  

- 서버가 켜지면 Spring Security 초기화 및 보안 설정이 이루어진다.
- 별도의 설정이가 구현을 하지 않아도 기본적 웹 보안 기능이 시스템에 연동된다.
  - 즉, 모든 요청은 인증이 되어야 자원에 접근이 가능하다.
  - 인증 방식은 Form 로그인 혹은 httpBasic 로그인 방식을 제공한다.
  - 이는 이후 Configuration을 통해 인증 없이 이용할 수 있는 서비스를 제어할 수 있다.

__Filter__ 라는 핵심 요소를 통해서 인증과 인가 처리를 대부분 진행한다.  

#### 인증(Authentication) 관련 Architecture
![spring security](https://user-images.githubusercontent.com/71700079/209428010-d477a81b-81a3-46f0-ae64-f0df28fece58.png)  

위의 사진과 같은 구조를 통해 인증을 진행하게 된다.  
자세하게 하나하나 분석을 해보자.  

1. Authentication Filter
  - 가장 먼저 HTTP Request가 들어오면 Filter가 가로채게 된다.
  - 해당 Filter에서 가로챈 정보를 통해 Token의 인증용 객체를 생성하게 된다.
2. UsernamePasswordAuthenticationToken
  - ```Principal-Credential``` 구조를 사용하는 인증용 객체이다.
  - 인증 이전의 객체를 생성하며, 인증이 완료된 객체 또한 생성하기도 한다.
  - 인증이 완료된 객체는 ```Authentication``` 객체라고 한다.
3. AuthenticationManager (Interface)
  - 해당 객체는 구현체가 아닌 Interface에 불과하다.
  - 이 Interface는 왜 존재할까?
    - 다음 절차인 ```AuthenticationProvider```이 종류가 여러 개이기 때문이다.
    - 해당 Provider에 대해서 OCP를 준수하기 위해 Interface를 둔다.
  - ```ProviderManager``` 라는 구현체를 통해 Provider들을 관리한다.
4. AuthenticationProvider
  - Manager은 인증용 객체인 Token을 여러 겹의 Provider에게 넘긴다.
  - 실질적인 인증 파트를 맡고 있으며, 인증되지 않은 Token을 인증하여 반환한다.
5. UserDetailedService
  - 실제로 DB에서 인증에 쓰일 사용자 정보를 가져온다.
6. UserDetails
  - DB의 사용자 정보를 통해 ```UserDetails```라는 객체가 생성된다.
  - 해당 객체를 Provider에게 다시 넘겨, 인증을 진행한다.
7. 인증이 완료되면 권한 등의 사용자 정보를 담은 ```Authentication``` 객체가 반환된다.
8. 다시 최초의 ```AuthenticationFilter```에게 ```Authentication``` 객체가 반환된다.
9. ```Authentication``` 객체를 ```Security Context```에 저장하고 마무리한다.

- ```Authentication``` 객체
  - Principal : 사용자의 ID혹은 User 객체
  - Credentials : 비밀번호
  - Authorities : 인증된 사용자의 권한 목록
  - Details : 인증 부가 정보
  - Authenticated : 인증 여부
  - ```Authentication```은 Interface로, 여러 구현체가 존재한다.
    - ```UsernamePasswordAuthenticationToken``` 도 그 중 하나이다.
- ```Security Context```
  - ```Authentication``` 객체가 저장되는 보관소이다.
  - ```ThreadLocal```에 저장되어 아무 곳에서나 참조가 가능하고, 언제든 꺼내 쓸 수 있다.

### 실전 Code 분석

위의 Code는 간단한 로그인 예제 Code이다.  
가장 먼저, DB에 접근해 해당 User가 존재하는 지 부터 간단히 검증한다.  

이후로, Security를 이용한 검증 단계가 진행된다.  
1. ```UsernamePasswordAuthenticationToken```을 통해 ```Authentication``` 객체를 만든다.
  - 이는 인증을 받기 이전의 객체이며, ```DTO```로 넘어온 User 정보를 통해 만든다.
2. ```ManageBuilder```의 ```authentication()``` 함수를 통해서 

> 참고 Github : [Hangangnow](https://github.com/HangangNow), [Flock](https://github.com/4ITING/Project-Flock)  
> 참고 Blog : [Catsbi's blog](https://catsbi.oopy.io/f9b0d83c-4775-47da-9c81-2261851fe0d0)  