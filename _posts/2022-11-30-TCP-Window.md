---
layout: post
title: "[PNUCSE Network] TCP Window"
categories: Network
tags: [theory]
author:
  - Jinseop Sim
toc: true
---
> 이 글은 부산대학교 탁성우 교수님의 컴퓨터 네트워크 강의 자료를 바탕으로 진행됩니다.  

### Stop and Wait ARQ  
![stopnwati](https://user-images.githubusercontent.com/71700079/205063716-3eb6b77d-83f1-4b7c-a7ca-a2589583095b.png)  

전송 계층에서 Reliable한 Segment 전송을 위해 사용하는 방식이다.  
Segment를 전송한 뒤, ACK을 수신 받아야만 다음 Segment를 전송할 수 있다.  

구현 방식이 매우 단순하며, 프레임 버퍼를 1칸만 사용해도 된다는 장점이 있다.  
위 이유에 따라 Sequence Number은 ```Modulo(2)``` 방식으로 0과 1만 사용할 수 있다.  

단점으로는, ACK을 기다리는 시간이 존재하기 때문에 Overhead가 크다는 점이 있다.  

### Go-back-N Protocol  
![GBN](https://user-images.githubusercontent.com/71700079/205063740-43fa6224-6ff5-4ab4-a50d-8dcea2c87fe4.png)  

이 또한 Reliable한 Segment 전송을 위해 사용하는 방식이다.  
Segment를 Window Size 만큼 보내다가, 특정 Sequence Number가 Loss가 발생하면  
ACK은 해당 번호에서 멈추며, 해당 Segment를 받을 때 까지 ACK가 늘지 않는다.  

이 방식은 Sequence number을 ```Modulo(2^N)```에 따라 번호를 매긴다.  
- Window Size는 ```2^N``` 보다 클 수 없다.
- Receiver Buffer Size는 1로 고정된다.
- Go-back-N Protocl의 Window Size가 1이 되면, Stop and Wait와 동일해진다.

### Selective Repeat(Reject) ARQ  
![SRA](https://user-images.githubusercontent.com/71700079/205063769-e8c6fff3-2fa8-48a3-a8e9-55edf43dc158.png)  

이 방식도 Sequence Number를 ```Modulo(2^N)```에 따라 번호를 매긴다.
- Window Size는 ```2^N``` 이어야만 한다.
- Receiver Buffer 또한 ```2^N``` 이어야만 한다.
  - 이 점이 GBN Protocol과 다르며, 유리한 점이다.
  - 다른 Packet들은 재전송이 필요 없어지기 때문이다.

Selective Repeat와 Go-Back-N은 Sliding Window Protocol이라고 부른다.  
Window가 Shrink하고 Increase하며 움직이는 모습이 마치 Slide하는 듯 보인다.  
