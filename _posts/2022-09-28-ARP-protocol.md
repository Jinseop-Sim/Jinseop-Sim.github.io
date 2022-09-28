---
layout: post
title: "[PNUCSE Network] ARP Protocol?"
categories: Network
tags: [theory]
author:
  - Jinseop Sim
toc: true
---
> 이 글은 부산대학교 탁성우 교수님의 컴퓨터 네트워크 강의 자료를 바탕으로 진행됩니다.  

저번 글까지는 Router에서 사용하는 IP Packet에 대해서 다뤄보았다.  
이번 글에서는 Ethernet에서 

### What is ARP?
ARP란 Address Resolution Protocol의 약자로, __주소 결정 프로토콜__ 이라는 의미이다.  
두 장치간에 Network 통신을 하기 위해서는, 실질적으로 목적지의 MAC 주소가 필요하다.  
그럼 해당 MAC 주소를 우리는 어떻게 알 수 있을까? 기계에 적힌 것을 보고 직접 해야할까?  

이럴 때 쓰이는 것이 바로 ARP Protocol! IP 주소를 통해서 MAC 주소를 알아내는 프로토콜이다.  

### ARP는 어떻게 동작할까?
ARP의 동작과정에 대해서 간략하게 알아보자.  
두 가지 경우로 나누어서 동작을 알아야 한다.  

- 같은 네크워크 안에서의 ARP 요청
  - 단말 A와 B가 통신을 하려 한다고 가정하자.
    1. A에서 B의 IP는 알고있으니, 현재 모르는 MAC 주소를 필요로 한다.
    2. 일단 MAC 주소를 ```FF:FF:FF:FF:FF:FF```(Broadcast)로 기록을 해놓고 Broadcast를 한다.
      - B의 IP를 가진 단말이 있나요? 그럼 혹시 MAC 주소 좀 알려주실 수 있나요?
    3. L2 Switch가 Broadcast된 요청을 받고 자신이 만약 Table에 B의 IP를 가지고 있다면, A에게 바로 응답해준다.
      - 가지고 있지 않다면? B에게까지 ARP 요청을 전달해서, B에서 직접 A에게 응답을 내려준다.
    4. 이제는 A가 B의 MAC 주소를 알게되었으니, Frame에 목적지 MAC 주소를 적어 통신이 가능하다!
- 다른 네트워크 간의 ARP 요청
  - 가정은 위와 같다. 하지만 다른 네트워크의 단말 B가 된다.
  1. A에서 B의 IP는 알고 있으니, 통신을 하려면 현재 모르는 MAC 주소가 필요하다.
  2. 위와 같이 MAC 주소를 ```FF:FF:FF:FF:FF:FF``` 로 기록을 해놓고 Broadcast를 한다.
  3. 이번엔 L2 Switch가 아닌 Router가 해당 Broadcast 요청을 받는다.
  4. 해당 요청을 받은 Router는 일단 B와 A가 다른 네트워크 대역에 존재하므로, 자신의 MAC 주소를 응답해준다.
  5. 그럼 응답을 받은 A는 Router의 MAC주소를 적어넣고, 다시 ARP 요청을 해당 Router에 보낸다.
  6. 하지만 Router도 B의 MAC를 모르는 것은 마찬가지! B의 네트워크 대역으로 Broadcast를 진행한다.
  7. 그럼 B가 목적지가 자신임을 알아차리고, 자신의 MAC 주소를 Router에게 응답해준다.
  8. 이제 Router를 통해 A와 B가 통신을 할 수 있게 되었다!

위의 동작을 보면 알 수 있듯이, ARP는 Broadcast-Enable 해야 사용이 가능함을 알 수 있다!  
따라서, __One-Hop 범위 내에 존재하는 단말과 통신하는 LAN 환경에서만__ 사용이 가능함을 알 수 있다.  