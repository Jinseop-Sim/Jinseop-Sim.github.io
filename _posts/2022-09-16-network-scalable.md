---
layout: post
title: "[PNUCSE 2022] Scalable Network"
categories: Network
tags: [theory]
author:
  - Jinseop Sim
toc: true
---
이 강의는 부산대학교 탁성우 교수님의 컴퓨터 네트워크 강의 자료입니다.  
저번 글에서는 컴퓨터 네트워크와 인터넷의 전체적인 구조에 대해서 적었다.  
이번 글에서는 컴퓨터 네트워크 연결의 확장성에 대해서 적어보려고 한다.  

### How to take Scalability?
__Scalability(확장성)__ 을 챙기는 데에는 두 가지 방식이 존재한다.

- Circuit Switching
[사진 첨부]
  - 회로처럼 하나의 회선을 할당받아서 Data를 주고받는 방식이다.
  - 할당받는 회선을 Dedicated Path라고 부른다.
	- 이 회선은 다른 사람이 절대 간섭할 수 없으며,
	- 단 둘을 위한 회선이기 때문에 목적지에 대한 정보가 필요가 없다.
	- 또한 송신하는 정보가 순서대로 도착한다.
- Packet Switching
  - Data를 Packet 단위로 쪼개서 Store & Forward 방식으로 전송한다.
  - 여러 개의 Router를 거쳐서 전송이 되기 때문에 부가적인 정보가 반드시 필요하다.
	- Header에 Destination IP Address가 반드시 필요하다.
	- 왜 IP Address 인가?
		- Router은 Network 계층까지 밖에 필요로 하지 않는다.
		- 따라서 IP Address를 통해서 목적지를 식별한다.
	- Destination IP Address만을 보고 Packet을 배달한다.
  - 전송되는 Packet은 순서와 상관없이 전송된다.