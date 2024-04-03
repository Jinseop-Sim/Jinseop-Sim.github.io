---
layout: post
title: "별점 기능 리팩토링 (w. Enum)"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
프로젝트에 수정할 부분이 없나 관찰하다보니, 별점 기능이 눈에 들어왔다.  
```Service``` 코드가 굉장히 길고, 메서드가 중구난방인 상태이다.  
아래의 사진에서 얼마나 복잡한지 느낄 수 있다.  

<img width="896" alt="스크린샷 2024-04-03 오후 5 54 23" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/14b688b2-b2d6-4824-b340-24cedaf3e225">  

이 부분을 리팩토링해서 더 깔끔하게 만들 수는 없을까?  
고민을 해보기로 결심했다.  

