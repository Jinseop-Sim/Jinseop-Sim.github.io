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
[사진 추가]

### 이상한 CORS
분명 서버 쪽 Configuration에도 아래와 같이 CORS에 대한 처리를 해놓았다.  
[사진 추가]

심지어 ```preflight request```도 성공적으로 보내지고 있다.  
응답에는 ```Access-Control Allow-Origin``` 헤더에 우리 사이트 Origin이 기록되어 있다.  
그렇다면 분명 이후에 해당 origin에서 들어오는 요청은 모두 통과가 되어야 한다.  
[사진 추가]

__왜 안되는 것일까?__