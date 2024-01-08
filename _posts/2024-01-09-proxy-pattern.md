---
layout: post
title: "Proxy pattern?"
categories: Tech
tags: [theory]
author:
  - Jinseop Sim
toc: true
---
이번에는 Proxy 패턴에 대해 학습해보자.  

### Proxy?
먼저 Proxy 패턴에 대해 간단하게 정의해보자.  
> 가벼운 작업을 대신 수행할 수 있는 대리인

위와 같이 간단하게 정의할 수 있을 것 같다.  
가벼운 작업에 대한 요청을 괜히 실제 클래스에게 맡기지 않고  
대리인 역할을 할 클래스가 처리하도록 하는 것이다.  

대리인 역할을 하는 클래스는 무거운 작업을 처리하지 않고  
무거운 요청이 들어오면 실제 클래스에게 위임하게 된다.  

### 사용 이유
