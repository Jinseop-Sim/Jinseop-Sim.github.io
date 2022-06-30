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
[사진 첨부]
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