---
layout: post
title: "HTTP에 대하여"
categories: Springboot
tags: [java]
author:
  - Jinseop Sim
---

## HTTP에 대하여

Spring Boot에 대해 배우기 이전에,  
간단하게 HTTP에 대하여 짚고 넘어가보자!  

### 모든 것이 HTTP
> *HyperText Transfer Protocol  

아래와 같은 형태의 Data들을 HTTP 규약에 따라 전송이 가능하다.  

- HTML, TEXT
- IMAGE, 음성, 영상
- JSON, XML(API)
- 거의 모든 형태의 Data 전송 가능

현재 HTTP/1.1 Version을 가장 많이 사용하고, 우리에게 중요하다.  
- HTTP/2 : 2015년, 성능 개선.
- HTTP/3 : TCP대신 UDP사용, 성능 개선, 현재 개발 중.

### HTTP의 특징
#### Clinet 서버 구조
- HTTP는 Request - Response 구조로 이루어진다.
- Client는 Server에 요청을 보내고, 응답을 대기한다.
- Server는 요청을 받아 결과를 만들어 응답한다.

#### Stateless Protocol
- Stateful : Server가 Clinet의 상태를 보존한다.
  - Client의 상태를 보존하기 때문에, Client는 요청을 끊어서 할 수 있다.
  - 하지만 Server는, 중간에 마음대로 바뀔 수 없다.
  - Server가 바뀔 때엔 상태를 모두 넘겨주고 바뀌어야 한다!

- Stateless : Server는 Client의 상태를 보존하지 않는다.
  - Server가 바뀌는 경우엔?
  - 어차피 Client가 필요 정보를 모두 담아서 보내기 때문에, 상관이 없다.
  - 갑자기 Client가 증가해도 Server를 대거 투입할 수 있다!
  
- Stateless의 실무 한계
  - 모든 것을 무상태로 설계할 수 없는 경우도 있다.
    - ex) 로그인, 상태 유지가 필요하다.
    - 일반적으로 쿠키, 세션 등을 생성해서 상태를 유지한다.
  - 하지만 최대한 무상태로 설계를 하도록 노력한다.

#### Connectionless
- 여러 Client들에 동시에 응답하는 경우엔 서버는 어떨까?
  - 동시에 여러 연결을 유지를 하고 있다면, 자원이 낭비될 것이다.
- 요청을 주고 받을 때만 연결을 유지하고, 다른 땐 끊어버린다면?
  - 서버는 최소한의 자원만을 사용할 수 있다.
- HTTP는 기본적으로 연결을 유지하지 않는 모델이다.
  - 일반적으로 초 단위의 빠른 속도로 응답을 하기 때문이다.
  - 1시간 동안 수천명이 서비스를 사용해도, 동시 처리되는 요청은 매우 적다.
- 한계점
  - 매번 TCP/IP 연결을 새로 맺어야 한다.
    - 3 Way Handshake Overhead (Client 입장에서 단점)
  - Web browser로 요청을 넣으면, HTML뿐만 아니라 CSS, 추가 이미지 등 수많은 자원이 동시에 다운로드 된다.
- 현재는 Persistent Connection으로 문제를 해결했다.

#### HTTP Message  

![화면 캡처 2022-06-30 203900](https://user-images.githubusercontent.com/71700079/176688505-4b919ec6-b2cc-4075-a51b-5cb6bc6b8b2c.png)  

- Start Line
  - Request Line : method request-target HTTP-version
    - Method : 서버가 수행해야 할 동작 지정
    - Target : 보통 절대 경로로 삽입. ('/'로 시작하는 경로)
  - Status Line : HTTP-version status-code reason-phrase
    - Status Code : 요청의 결과 상태를 나타내는 Code. 
    - Reason Phrase : 상태 코드에 대한 간결한 설명.
- Header
  - field-name: (OWS) field-value (OWS) 와 같은 형태.
    - OWS는 띄어쓰기를 허용함을 의미한다.
  - HTTP 전송에 필요한 모든 부가정보가 포함된다.
    - Body의 내용, 크기, 압축
    - 인증, 요청 클라이언트 정보 ..
- Body
  - 실제 전송할 Data가 담긴다.
  - Byte 형태로 저장 가능한 모든 정보가 담김!

### HTTP Method
#### API는 어떻게 만들어질까?
- API URI 설계
  - 회원 목록 조회 /read-member-list
  - 회원 조회 /read-member-by-id
  - 회원 등록 /create-member
  - 회원 수정 /update-member
  - 회원 삭제 /delete-member

위 설계 방식은 과연 좋은 설계인가? 그렇지 않다!

- Resource의 의미는 무엇일까?
  - 회원에 대한 동작이 Resource가 아니다.
  - 회원 그 자체가 Resource가 된다.
  - 그에 맞춰서 Resource 중저  URI를 설계 해보자.

- Resource 중점의 URI 설계
  - 회원 목록 조회 /members
  - 회원 조회 /members/{id}
  - 회원 등록 /members/{id}
  - 회원 수정 /members/{id}
  - 회원 삭제 /members/{id}

이렇게 설계를 하면 조회, 등록, 수정, 삭제를 어떻게 구분할 것인가?
그럼 Resource(명사)와 행위(동사)를 분리하자!

#### HTTP Method의 종류
- GET : Resource 조회
  - 전달하고자 하는 Data를 Query Parameter(String)으로 전달.
  - Body로도 전달 가능하지만, 지원하는 곳이 많이 없다.
- POST : 요청 Data 처리. 주로 등록하는 데에 사용.
  - Body를 통해서 요청 Data를 전달한다.
  - 주로 신규 Resource 등록, Process 처리에 사용한다.
  - POST 요청이 오면, 어떻게 처리할지 Resource 마다 정해주어야 한다.
  - 어떤 Method로 처리하기 애매한 경우에, 그냥 POST를 쓰기도 한다.
- PUT : Resourec를 대체, 없으면 새로 생성
  - 기존 Resource를 덮어쓰기(완전 대체) 한다.
    - ex) name과 age가 이미 있을 때, age만 보내면 name은 없어진다.
  - POST와의 차이는, Client가 리소스 URI를 지정한다는 것!
- PATCH : Resource 수정
  - Patch는 Put과 다르게 Resource 중 일부만 보내도 수정이 된다.
  - 만약 Patch가 지원이 안되는 서버라면 Post를 쓰자!
- DELETE : Resource 삭제
- HEAD : GET과 유사하지만, Header와 Status Line만 반환.
- OPTIONS : 통신 가능한 Method 목록을 반환

#### HTTP Method의 특징
- Safe
  - 호출해도 Resource를 변경하지 않는다.
  - GET이 안전한 Method 중 하나이다.
- Idempotent
  - 몇 번을 호출해도 결과가 바뀌지 않는다.
  - GET, PUT, DELETE 가 멱등 Method이다.
  - POST는 멱등성이 없다!
    - 중복 호출 시, 같은 결제가 중복해서 발생할 수가 있다!
  - 자동 복구 메커니즘에 활용되는 성질이다.
    - 서버가 정상 작동을 하지 않을 때, 같은 요청을 받아도 되는가?
- Cacheable
  - 응답 결과 Resource를 Cache해서 사용해도 되는가?
  - GET, HEAD, POST, PATCH는 Cache 가능.
    - 하지만 실제로는 GET, HEAD 정도만 사용한다.
    - POST와 PATCH는 본문 내용까지 고려를 해야해서 구현이 어렵다.
