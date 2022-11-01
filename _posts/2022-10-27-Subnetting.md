---
layout: post
title: "[PNUCSE Network] Subnetting"
categories: Network
tags: [theory]
author:
  - Jinseop Sim
toc: true
---
> 이 글은 부산대학교 탁성우 교수님의 컴퓨터 네트워크 강의 자료를 바탕으로 진행됩니다.

이번 시간에는 IP에 관해서 또 다른 얘기를 해보려고 한다.  
바로 IP 할당에 관한 또 다른 얘기이다.  

### Class Based IP
앞서 NAT 강의에서 말했듯이, IP는 원래 Class에 따라 할당된다.  
하지만 그냥 Class로만 할당을 하게 되면, IP를 낭비하는 경우가 발생하게 된다!  

예를 들어, 내가 260개의 Host를 운영하고 싶어서, Class C의 IP를 2개 산다면,  
하나의 Class에 255개를 모두 쓰고 남은 5개만 다른 Class에 쓸 것인데,  
이는 곧 250개의 IP가 낭비가 되고 있다는 말이 된다!  
따라서 이에 대한 해결책이 필요하다.  

### Limit of Router Table
위의 IP 낭비로 인해서, IP와 Next hop을 저장하고 있어야 할 Router Table이 부족해진다.  
Router Table이 커지면 커질 수록, Lookup 시간은 길어질 것이고 당연히 속도도 느려질 것이다.  
따라서 Router Table을 최대한 줄이며 IP를 낭비하지 않을 방법이 필요하다!  

### Subnetting
위와 같은 이유들로 인해서 등장한 것이 바로 __Subnetting__ 이다.  
기본적으로 하나의 네트워크를 여러개의 서브 네트워크로 자르는 방식이다.  

아래와 같은 방식으로 네트워크를 더 작게 분할한다.  
![image](https://user-images.githubusercontent.com/71700079/199285422-989a97ed-b4e5-4335-84d9-667cf1e85371.png)  
![image](https://user-images.githubusercontent.com/71700079/199285587-baf5c4e1-5e28-4299-a2ea-e851c9a332b3.png)  

분할을 하게 되면, IP의 낭비가 없게되며 Router Table의 크기도 줄일 수 있게 된다.  

### Supernetting
__Supernetting__ 은 Subnetting과 정반대의 개념이다.  
