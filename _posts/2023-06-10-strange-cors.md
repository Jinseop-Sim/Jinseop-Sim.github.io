---
layout: post
title: "CORS의 기이한 오류"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
토이 프로젝트가 어느새 막바지에 다다르고, 실제 동작을 확인하는 절차만 남았다.  
그러나, 확인을 하는 절차를 진행하지 못할 근본적인 문제가 생겼다.  
아래와 같이 이전에 잡았던 __CORS__ 에러가 발생하는 것이다.  
![CORS](https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/235dd616-e512-49fe-8d23-d82379119a11)  

### 이상한 CORS
분명 서버 쪽 Configuration에도 아래와 같이 CORS에 대한 처리를 해놓았다.  
[사진 추가 예정]

심지어 ```preflight request```도 성공적으로 보내지고 있다.  
응답에는 ```Access-Control Allow-Origin``` 헤더에 우리 사이트 Origin이 기록되어 있다.  
그렇다면 분명 이후에 해당 origin에서 들어오는 요청은 모두 통과가 되어야 한다.  
![preflight](https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/fea7efff-e281-480d-be4b-207072e3489d)  

__왜 안되는 것일까?__  

### 만료된 Access Token?
혹시 로그인 관련된 에러이기 때문에 토큰과 관련된 문제인지를 의심해 보았다.  
```LocalStorage```를 확인해 보았는데, 만료된 AccessToken으로 계속 요청이 들어감을 확인했다.  

따라서 나는 아래의 두 가지 상황을 가정했다.  
1. 만료된 AccessToken을 가지고 요청을 보내, ```Access-Control-Allow-Origin``` 값이 넘어오기 전에 ```401```을 반환해 CORS가 발생하는 것이다.
2. 혹은 반대로 CORS가 발생했기 때문에, AccessToken이 재발급 되지 못하고 만료된 상태로 남아있는 것이다.  

위의 상황들을 확인하기 위해서는 서버의 ```FilterChain```에서 CORS와 토큰 만료 중  
어느 쪽을 우선시해서 예외를 발생시키는지를 확인하면 해결이 될 것이다.  
