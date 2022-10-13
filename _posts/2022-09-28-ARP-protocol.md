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

### Why ARP is Awesome?! 
- Dynamic Mapping이 가능하다!
  - Dynamic Mapping이라는 말은, 물리 주소나 논리 주소 둘 중 하나만 알아도 나머지 하나가 요청이 가능하다는 의미이다.
  - 이는 Application과 User에 대해 생각하지 않아도 된다는 의미이다.
  - 또한 System 관리자에 대해서 생각하지 않아도 된다는 의미이다.
- Network Layer Protocol을 이용하지 않아도 된다.
  - Data Link Layer에서 지원하는 Protocol이다.
- Broadcasting이 가능한 Data Link가 필요하다!
  - Broadcast가 가능하다는 의미!

### Ethernet Frame
ARP는 지근거리 통신에서만 사용가능하다. 따라서 우리가 현재 사용하는 LAN인 Ethernet도 ARP를 이용하고 있다!  
그렇다면 Data를 Network 상에서 옮길 때 Ethernet의 Frame은 어떤 형태로 붙을까?

우리는 Data를 전송할 때, 각 계층마다 Header를 붙여서 Lower Layer로 내려준다는 사실을 알고 있다.  
그럼 사실상 전송하기 전 최종 계층인 Data Link 계층에서 Frame을 Packet에 붙이게 될텐데, 어떤 형태로 붙게 될까?  
![이더넷프레임](https://user-images.githubusercontent.com/71700079/193004853-21521993-33ef-4eef-8be1-50565d07104b.png)  

- Preamble : 송신측과 수신측의 비트 동기화를 위해 사용한다.
  - 총 8byte로 구성되며, Sync Bit(7byte)와 SFD(1byte)로 나눠진다.
  - 이 기능 덕분에 우리는 수신측에서 Data의 출발 및 도착 시간 동기화를 할 필요가 없어졌다.
- Source / Dest Address : 출발 및 도착 장치의 MAC 주소이다.
- Type
  - Ethernet II의 표준으로는, 상위계층 Protocol의 정보를 담는다.
  - IEEE 802.3 CSMA/CD의 표준으로는, MAC 프레임의 길이를 담는다.
  - 해당 영역 값이 ```0x0600``` 이상이면 Ethernet II로, 아니면 IEEE 802.3 표준으로 해석한다.
- Data : 실제로 우리가 전송하는 컨텐츠
  - 이전 글에서 말했던 MTU(Max Transmission Unit)에 따라 최대 1500byte가 가능하다.
  - 최소 46byte로 구성되며, 46byte가 채워지지 않을 시 Padding으로 46byte를 맞춘다.
    - 왜?
- CRC(Cyclic Redundancy Check) : IPv4 Packet에 있던 Checksum과 같이 오류를 검사하는 비트이다.
  - 나머지는 모두 Frame Header에 붙는데, 이 친구는 Frame Trailer에 붙게 된다.
  - 오류가 검출될 시, 즉시 Frame을 Drop해버린다.

### ARP Table
ARP도 HTTP와 같은 Cache의 개념을 차용해서 사용하고 있다.  
MAC Address를 알 수 없는 장치의 IP와 MAC Address를 처음 연결 시 Table에 저장을 한다.  
이는 Cache와 같은 역할을 하며, 다음 연결 시 빠른 연결을 제공한다.  

### Proxy ARP
Proxy(가짜) ARP이다.  

냉장고를 예시로 들어보자.  
나는 Internet이 삽입되어있지 않는 냉장고를 사용한다. 이 냉장고는 USB만 존재한다.  
IP Address는 있으나, MAC Address가 존재하지 않아서 ARP 통신이 불가능하다!  

이 때 Ethernet Card가 있어서 ARP 통신이 가능한 PC로 ARP 응답을 보낼 수 있다.  
내 스마트 폰에서, 특정 IP의 MAC 주소를 찾는 ARP 요청을 보낸다.  
그럼 원래는 냉장고에게서 MAC 주소를 받아와야 하지만, PC가 본인인 척 요청을 가로챈다.  
요청을 가로챈 뒤 내가 해당 IP의 주인이고 MAC 주소는 이러이러하다라고 응답을 보낸다.  

이후, PC와 냉장고를 USB로 연결해 Relay Program을 이용해서 정보 교환이 가능하다.  

이것은 유용한 방식일 수도 있겠지만, ARP가 보안에 취약하다는 반증이 된다.  
누구든 자신이 해당 IP의 주인이라며 Packet을 채갈 수 있다는 것이다.  
이는 ARP 요청이 Broadcast로 항상 진행되기 때문이다.  

### Gratuitous ARP
GARP는 보통 ARP와는 다르게 MAC 주소의 획득에 목적을 두지 않는다.  
내 IP를 목적지로 요청을 보내는데, 대표적인 목적 2가지는 아래와 같다.

- IP 주소 충돌 감지
  - 내 IP를 목적지로 ARP 요청을 보냈는데 누군가 응답을 한다면?
  - 이미 해당 IP를 사용하는 Host가 존재한다는 말이 된다.
    - 이 경우엔, 해당 Host를 막을 방법은 따로 없으며 Windows는 경고만 하도록 되어 있다.
- ARP Table 갱신
  - 누군가가 GARP Packet을 보내면, 이를 수신한 모든 Host나 Router은 Table을 갱신한다.

### RARP
ARP는 IP 주소를 알 때, 알 수 없는 MAC 주소를 알아오는 Protocol 이었다.  
그럼 그 반대로 MAC 주소는 있지만 IP가 할당되어있지 않을 때는 어떻게 해야할까?  

RARP(Reverse ARP) 방식을 옛날에는 사용했다.  
ARP와는 반대로, 목적지의 MAC 주소를 바탕으로 IP 주소를 받아오는 Dynamic Mapping Protocol이다.  

하지만 RARP는 분명한 한계가 존재했다!  
- IP 주소 밖에 받아올 수 없다.
- 모든 Link 마다 서버가 존재해야 IP를 받아올 수 있다.
- OS에 존재하지 않고, 물리 계층에 존재하기 때문에 계층 원리에 어긋난다.

### BOOTP
위와 같은 한계로 인해 RARP의 발전된 Protocol인 BOOTP가 등장하게 된다.  
BOOTP(Bootstrap Protocol)은, RARP 보다 쉽게 개발을 하기 위해 상위 계층과 통신을 한다.  
__UDP/IP Packet__ 을 이용하며, __Network__ 계층에서 통신을 진행한다.

### DHCP
BOOTP에 더불어서 최종적으로 발전하게된 현재의 IP 할당 Protocol이다.  
중앙에 DHCP Server를 두고, 해당 네트워크 내의 Host들에게 IP를 모두 할당해주도록 한다.  
그럼 굳이 IP를 요청할 일이 없으며, RARP 요청이 전혀 필요가 없게 된다!  

##### DHCP Procedure
![dhcp 동작](https://user-images.githubusercontent.com/71700079/195479651-dc0acb75-db66-4a8b-9769-81a5f4415754.png)

위와 같은 동작을 통해서 DHCP Server에서 IP를 할당받는다.  
그리고 위 과정을 거치며, Client는 총 6개의 State를 거쳐간다.  
- ```INITIALIZE``` : 가장 초기의 상태이다.
- ```SELECT``` : Client가 Broadcast로 ```DISCOVER```을 날린 상태이다.
- ```REQUEST``` : DHCP 서버로 부터 ```OFFER```를 받은 상태이다.
  - 이제, 해당 서버에게 IP 할당을 받기 위해 Request 메시지를 날린다.
- ```BOUND``` : DCHP 서버로 부터 마지막 ACK을 받고 IP를 할당받은 상태.
  - 더 이상 IP를 쓰고 싶지 않다면?
  - ```RELEASE```를 보내 ```INITIALIZE``` 상태로 돌아갈 수 있다!
- ```RENEW``` : IP 주소를 갱신할 수 있는 상태
  - IP를 할당 받으면, 해당 IP를 사용할 수 있는 Lease Time이 존재한다.
  - 이 시간이 50% 이상 지났으면, 연장을 하기 위해 Request를 날린다.
  - 이후 ```RENEW``` 상태에 들어가는 것!
    - 여기서 ACK을 받는다면, 다시 ```BOUND``` 상태가 되는 것이고,
    - ACK이 아닌 NACK을 받는다면, ```INITIALIZE``` 상태로 돌아가 다시 할당을 받는다.
- ```REBIND``` : IP 주소를 갱신해야만 하는 상태
  - 위의 ```RENEW```를 지나서, Lease time의 87.5% 까지 응답을 받지 못했다면?
  - Request를 날리고, ```REBIND``` 상태로 들어간다.
    - 이 때도 ```RENEW```와 같이, ACK과 NACK에 따라 다르게 동작한다.

### Relay Agent
Relay Agent. 이 용어는 DHCP의 확장성과 관련된 용어이다.  

우리는 최초에 DHCP 서버를 찾기 위해 Broadcast로 Discover을 날린다.  
하지만 분명 Broadcast는 LAN, 즉 같은 네트워크 안에서만 가능하다.  
그런데 만약 DHCP 서버가 같은 네트워크 안에 존재하지 않는다면?  

그럴 때 필요한 것이 바로 __Relay Agent__ 이다.  
DHCP Relay Agent가 Broadcast 메시지를 받아서 DHCP Server에게 Unicast로 전달한다!  