---
layout: post
title: "Composite Pattern?"
categories: Tech
tags: [theory]
author:
  - Jinseop Sim
toc: true
---
> __모든 글은 유튜브 얄팍한 코딩사전에 출처를 두고있습니다.__

이번에 알아볼 패턴은 __Composite__ 패턴이다.  

### Composite?
Composite는 합성물이라는 의미를 가진다.  
그 의미에 맞도록 먼저 간단하게 아래와 같이 정의할 수 있겠다.  

> 복합 클래스와 단일 클래스를 동일 취급하기 위한 패턴!  

사용하는 이유를 알아보며 더 자세히 알아보자.  

### 사용 이유
가장 대표적인 예시로 파일 시스템을 들 수 있을 것 같다.  
파일 시스템을 생각해보면, 폴더와 파일은 동일한 기능을 한다.  
이름을 가지고 있으며, 삭제가 가능하다는 점에서 그렇다.  

그럼 둘은 분명 구현 시 중복되는 코드가 존재할 것이다.  
예컨대 ```name```과 같은 멤버 변수를 중복으로 가질 수 있겠다.  
또한 ```getSize()```나 ```remove()``` 같은 동작도 가질 수 있다.  
이를 따로따로 코딩하는 것 보단, interface로 묶는 것이 훨씬 깔끔할 것이다.  

바로 이럴 때 사용되는 패턴이 __Composite!__  

### 구현
이번 패턴은 설명 없이 바로 구현부를 보도록 하자.   

<img width="463" alt="스크린샷 2024-01-12 오후 8 21 38" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/f564b0fb-353b-4813-895f-6b3363f5ce15">  

위와 같이 먼저 ```File``` 클래스를 구현할 수 있다.  
```FileSystem``` interface를 구현했음을 볼 수 있으며,  
아래로는 ```Folder``` 클래스를 구현한 코드이다.  

<img width="613" alt="스크린샷 2024-01-12 오후 8 22 32" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/824d06aa-3dde-4bac-aaff-74bdbd9eb49e">  

폴더는 Composite(복합) 객체, 파일은 Leaf(단일) 객체가 된다.  
둘은 ```getSize()```와 ```remove()```의 동일 동작 수행이 가능하다.  
따라서 interface로 묶어내어 보다 깔끔하게 코드를 짤 수 있다.  

실제 사용은 아래와 같이 사용할 수 있을 것이다.  

<img width="717" alt="스크린샷 2024-01-12 오후 8 24 07" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/e04eb713-37da-46f7-bcda-59d56c6f1f41">  

여러가지 폴더를 생성하고, 폴더안에 폴더를 넣거나 파일을 집어 넣었다.  
실제 파일 시스템과 동일한 구조라고 보면 될 것 같다.  
그 결과 ```getSize()```를 폴더에 대해 실행하면 아래와 같이 결과가 출력된다.  

<img width="681" alt="스크린샷 2024-01-12 오후 8 25 14" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/a0bfbe09-cc44-4fb1-9905-22dcb2a5f372">  

각 파일의 크기가 출력되며, 파일이 포함된 폴더들의 용량도 모두 출력된다.  
이렇게 복합 클래스와 단일 클래스에 중복되는 동작이 가능한 상황에,  
우리는 __Composite__ 패턴을 통해 깔끔한 클린 코드를 작성할 수 있다.  
