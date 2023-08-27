---
layout: post
title: "Vue의 생명주기 훅"
categories: frontend
tags: [nodejs, vuejs]
author:
  - Jinseop Sim
toc: true
---
프론트엔드 개발을 하건, 백엔드 개발을 하건 언제든 우리는  
생명주기에 관해서 이해하고 관리할 줄 알아야 할 때가 분명히 생긴다.  

나는 이번에 졸업과제를 진행하며 프론트엔드 개발을 진행 중이다.  
이 때, Vue를 사용하면서 생명주기를 관리한 경험을 적어보려고 한다.  

## 생명주기 훅(Hook)이란?
위에서 언급했듯이, 모든 것에는 생명 주기가 존재한다.  
Vue에서는 __생명주기 훅__ 이라는 함수를 통해서 생명주기를 관리한다.  
생명주기에 따라 어떤 함수가, 어떤 DOM을 어떻게 할 지 관리하는 것이다.  

Vue에는 아래와 같은 생명주기로 페이지가 생성되고 소멸된다.  
각각의 생명주기는 ```composition API``` 환경에서 어떤 함수로 관리될까?  

![image](https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/fdbef035-349f-4430-a07d-f674d7b54af9)  
> 출처 : [Lifecycle Hooks | Vue.js](https://vuejs.org/guide/essentials/lifecycle.html#lifecycle-diagram)

### Created
### Mounted
### Updated
### Unmounted

## 프로젝트로의 적용
졸업과제 프로젝트에서도 해당 함수들을 통해 생명주기를 관리했다.  
아래의 예시는 지도 기능에서 ```Mounted``` 함수를 사용한 예시이다.  
<img width="306" alt="스크린샷 2023-08-28 오전 12 15 48" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/042006b4-9a73-4a9c-a69b-e81eaf28b8de">  
