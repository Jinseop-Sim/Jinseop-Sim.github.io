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

### Reliable and Ordered Delivery
TCP Buffer에는 그림과 같은 3가지 Pointer가 존재한다.  
- Sender
  - LastByteAcked : ACK을 몇 번 까지 받았는지 저장한다.
    - 당연히 LastByteSent 보다는 작거나 같다.
  - LastByteWritten : Application이 Buffer의 어디까지를 썼는지 저장한다.
    - 가장 앞에 있으며, LastByteSent보다 크거나 같다.
  - LastByteSent : Buffer에 저장된 Data를 어디까지 보냈는지 저장한다.
- Receiver
  - LastByteRead : Application에서 마지막으로 읽어 사용한 Data의 위치이다.
    - 방금 도착한 Data는 아직 읽지 않으므로, LastByteRcvd 보다 작아야 한다.
  - NextByteExpected : 아직 오지 않아서 받아야하는 Data의 위치를 저장한다.
    - 중간에 Loss가 발생했으면, 그 위치를 가리키게 된다.
    - LastByteRcvd + 1 보다 작거 같을 수 있다.
      - ```+1```이 기준인 이유는, ACK은 Seq보다 항상 +1 되어 도착하기 때문이다.
  - LastByteRcvd : 수신 버퍼에 저장된 Data의 마지막 위치를 저장한다.  

위의 정보들을 통해서 아래와 같은 특징들을 알아낼 수 있다.  
- ```LastByteRcvd - LastByteRead <= Sizeof(MaxRcvBuffer)```
  - Receiver의 Buffer에 도착하는 Data Stream은 최대 버퍼를 넘을 수 없다.
  - 이는 Buffer overflow로 부터 안전하게 보장하기 위함이다.
- ```AdvertisedWindow = MaxRcvBuffer - {(NextByteExpected - 1) - LastByteRead}
  - Receiver Buffer에 남은 공간을 의미하는 식이다.
  - 사실 더 정확하게 계산하려면, Loss가 발생한 Data 까지 계산해주어야 한다.
    - ```The amount of out-of-order data received since the NextByteExpected```
- ```EfectiveWindow = AdvertisedWindow - (LastByteSent - LastByteAcked)```
  - 해당 값이 0이 되면, Zero Window로 Data를 더 이상 전송할 수 없게 된다.
- 해당 정보를 이용해서 TCP는 Flow Control을 구현할 수 있다.

### Silly Window Syndrome
TCP에서는 기본적으로 Sliding Window Algorithm을 사용해 Buffer를 관리한다.  
그 과정에서 일어날 수 있는 이상한 문제를 하나 알아보자.

### Nagel Algorithm
위의 Silly Window Sydrome을 해결할 수 있는 해결책이 있다.  
바로 __Nagel Algorithm__ 이다.  