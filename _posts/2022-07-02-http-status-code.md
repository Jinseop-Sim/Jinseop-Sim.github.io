---
layout: post
title: "HTTP 상태코드"
categories: Springboot
tags: [java]
author:
  - Jinseop Sim
---

> HTTP Response에서, Client가 보낸 요청의 상태를 알려주는 Code이다.  

### 1XX (Informational)
요청이 수신됐고, 처리중이라는 의미로 잘 쓰지 않는다.

### 2XX (Successful)
요청이 정상적으로 처리되었다는 의미의 Code이다.  

- 200 OK
  - 가장 흔한 응답 Code로 정상적으로 처리 된 것.
- 201 Created
  - 요청에 성공해서 새로운 Resource가 생성되었다는 것.
  - 생성된 Resource는 응답의 Location Header로 식별한다.
- 202 Accepted
  - 요청이 접수는 되었으나, 처리가 완료되지 않음.
  - ex) 요청 접수 후 1시간 뒤에 배치 프로세스가 요청을 처리할 예정.
- 204 No content
  - 요청을 성공적으로 수행은 했다.
  - 하지만 응답 Payload Body에 보낼 Data가 없다.
  - ex) 저장 버튼의 경우에는, 결과에 아무 Data가 없어도 된다.

### 3XX (Redirection)
요청을 완료하기 위해선 추가적인 동작이 필요하다는 의미이다.  

이 때 웹 브라우저는 Response에 Location Header가 존재하면,  
자동으로 해당 Location으로 Redirect한다.  

[사진 첨부]

#### Redirection의 종류
- 영구 Redirection : 특정 리소스의 URI가 영구 이동.
  - 301 Moved Permanently
    - Redirect 요청 Method가 GET으로 변하고 본문이 제거될 수도 있다.
  - 308 Permanent Redirect
    - 301와 동작은 같지만, Method와 본문이 유지된다.
  - ex) /members -> /users
  - ex) /event -> /new-event
- 일시 Redirection : 일시적인 이동.
  - 주로 일시 Redirection을 매우 많이 사용한다. 특히 302.
  - 302 Found
    - Redirect 요청 Method가 GET으로 변하고, 본문이 제거될 수도 있다.
  - 307 Temporary Redirect
    - Redirect 요청 Method와 본문이 변하지 않는다.
  - 303 See Other
    - 302와 기능은 같으나, Method를 무조건 GET으로 바꾼다.
  - ex) 주문 완료 후 주문 내역 화면으로 이동.
  - ex) PRG : Post / Redirect / Get
- 특수 Redirection
  - 304 Not Modified
    - Cache를 목적으로 사용한다.
    - Client에게 리소스가 수정되지 않았음을 알려주는 것.
    - 따라서 Client는 로컬에 존재하는 Cache를 재사용하도록 한다.
    - 304 응답은 Body를 포함해서는 안된다.
    - 조건부 GET, HEAD 요청시 사용한다.

#### PRG : Post / Redirect / Get
만약 POST로 주문 후에, 웹 브라우저를 새로고침 한다면?  
새로고침은 다시 요청을 보내는 것이기 때문에 중복 주문의 위험이 있다.  

따라서 아래와 같이 조치한다.  
- POST로 주문 후에 주문 결과화면을 GET으로 Redirect 시킨다.
- 새로고침해도 결과 화면은 GET으로만 조회 된다.

물론, 서버 단에서 중복 주문을 못하도록 검증하는 단계를 만드는 것 또한 필요하다.  

### 4XX (Client Error)
잘못된 문법, 입력 등으로 Clinet의 오류로 서버가 정상 작동할 수 없는 경우이다.  

- 400 Bad Request
  - 요청 구문, 메시지 등의 오류
  - Client는 요청 내용을 다시 검토하고 보내야 한다.
  - ex) 요청 파라미터 오류, API 스펙 오류
- 401 Unauthorized
  - 인증이 되지 않았다.
  - 응답에 __WWW-Authenticate__ 헤더와 함께 인증 방법을 설명한다.
- 403 Forbidden
  - 인증 자격은 있지만, 접근 권한이 불충분하다.
  - ex) ADMIN 등급이 아닌 사용자가 로그인은 했지만, 권한이 없음.
- 404 Not Found
  - 요청 리소스가 서버에 없음.
  - 또는 Client가 권한이 없을 때, 해당 리소스를 숨기고 싶을 때도 씀.

### 5XX (Server Error)
서버 오류로 인해 서버가 정상 요청을 처리하지 못하는 경우이다.  
서버가 복구가 된다면, 다시 접근이 가능하다.  

- 500 Internal Server Error
  - 서버 내부 문제로 오류가 발생한 것이다.
  - 애매하면 500 오류를 낸다.
- 503 Service Unavailable
  - 서버가 일시적으로 과부하가 됐다거나, 예정된 작업이 있다는 것.
  - 응답에 Retry-After Header로 복구 예상 시간을 알려줄 수 있다.

진짜 정말로 서버에 문제가 발생한 것이 아니라면,  
웬만하면 5XX 에러를 발생시키지 말자!  
웬만하면 Client의 오류로 판단하에 4XX 에러를 발생시켜야 한다.  

### 만약 모르는 Status Code가 반환된다면?
갑자기 299 451 599 처럼 알 수 없는 Status Code를 반환 받는다면?  
당황하지 말고, 제일 첫 자리인 상위 Status Code만 떠올리자.