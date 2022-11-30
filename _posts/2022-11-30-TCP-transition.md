---
layout: post
title: "[PNUCSE Network] TCP Transition Diagram"
categories: Network
tags: [theory]
author:
  - Jinseop Sim
toc: true
---
> 이 글은 부산대학교 탁성우 교수님의 컴퓨터 네트워크 강의 자료를 바탕으로 진행됩니다.  

![image](https://user-images.githubusercontent.com/71700079/204078596-962b3f8f-2da4-4b96-b49f-c27704acc723.png)  
TCP는 위와 같은 Finite State Machine의 모습을 갖는다.  

- Client 입장에서의 상태
  - CLOSED : 최초의 시작 상태이다.
    - Active Open 요구를 수신할 수 있다.
  - SYN-SENT
    - TCP 응용 프로그램으로부터 Active Open 요구를 수신한다.
    - 이후, SYN Segment를 Server로 전송하고, SYN-SENT 상태로 천이한다.
  - ESTABLISHED
    - SYN-SENT 상태의 Client가 SYN + ACK Segment를 서버로 부터 수신한다.
    - Client가 ACK을 Server로 전송한 뒤 ESTABLISH 상태로 천이한다.
  - FIN-WAIT-1
    - ESTABLISHED, 연결이 된 상태에서 ```close()``` 요구를 수신한다.
    - 즉시 FIN-WAIT-1 상태로 천이하며, ACK의 수신을 기다린다.
  - FIN-WAIT-2
    - Server에서 ACK을 보내오면, FIN-WAIT-2 상태로 천이한다.
    - 이제는 마지막인 FIN Segment를 기다린다.
  - TIME-WAIT
    - FIN Segment가 도착하면, Client는 ACK을 전송하고 TIME-WAIT 상태로 천이한다.
    - 이 WAIT TIME은 2MSL(Max Segment Lifetime, normaly 2min)으로 설정된다.
- Server 입장에서의 상태, 정상적인 Server라면 아래와 같은 상태를 갖는다.
  - CLOSED : 최초의 시작 상태이다.
    - Passive Open 요구를 수신할 수 있다.
  - LISTEN
    - Passive Open 요구를 수신한 Server는 LISTEN 상태로 천이한다.
    - LISTEN 상태의 Server는 SYN Segment를 기다린다.
  - SYN-RCVD
    - Client로 부터 SYN Segment를 수신하면, SYN + ACK Segment를 전송한다.
    - 이후 SYN-RCVD 상태로 천이한다.
  - ESTABLISHED
    - SYN-RCVD 상태의 Server가 ACK을 수신하면, ESTABLISHED 상태로 천이한다.
    - 연결이 되었다는 의미이다.
  - CLOSE-WAIT
    - ESTABLISHED 상태의 Server가 FIN Segment를 수신한다.
    - ACK Segment를 Client로 보낸 후 CLOSE-WAIT 상태로 천이한다.
    - 이 상태에서는 서버 프로그램의 종료 요구 수신을 기다린다.
  - LAST-ACK
    - 서버 프로그램의 종료 요구가 도착하면, Client에 FIN을 전송한다.
    - 이후 LAST-ACK 상태로 천이하며, ACK Segment를 기다린다.
    - ACK Segment를 수신하면 CLOSED 상태로 천이한다.

### Why Time wait?
Packet의 오동작을 막기 위해서 Time wait를 통해 한번 더 기다린다!  

2MSL를 기다리는 동안에 오는 마지막 Packet을 완전히 전송하기 위해 기다린다.  
해당 시간 동안 Client에게서 응답이 없으면, 연결을 완전히 끊는다.  

결국, TCP의 Full Duplex 연결 종료를 Reliable하게 구현하기 위해 사용한다.