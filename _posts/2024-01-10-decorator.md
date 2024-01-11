---
layout: post
title: "Decorator pattern?"
categories: Tech
tags: [theory]
author:
  - Jinseop Sim
toc: true
---
> 모든 글은 유튜브 얄팍한 코딩사전님에 출처를 두고있습니다.  

이번 패턴은 __Decorator__ 패턴이다.  

### Decorator?
Decorator, 의미 그대로 장식을 담당하는 패턴이다.  
먼저 간단하게 아래와 같이 정의할 수 있을 것 같다.  

> 깔끔하게 다양한 추가 기능을 조합할 수 있도록 하는 패턴!

말이 생각보다 어려운 것 같다.  
사용하는 이유를 설명하며 더 자세히 알아보자.  

### 사용 이유
만약 기존의 기능이 있는데, 새로운 기능을 추가하고 싶다고 해보자.  
그런데 그냥 추가하는 것이 아닌, 기능을 조합하고 싶다.  

예를 들어 아래와 같이 전투기 게임을 한다고 생각해보자.  
이 전투기는 아이템을 먹으면 추가적으로 무기가 부착된다.  

<img width="485" alt="스크린샷 2024-01-11 오후 7 34 49" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/7708a436-cba9-4790-bd81-b5af651cbc2b">  

이 때, 추가적으로 무기를 사용하도록 하는 클래스를 만약  
아래와 같은 구조로 매번 새 클래스를 선언한다면?  
무기의 종류가 많아지면 많아질수록 복잡도가 매우 커질 것이다.  

<img width="529" alt="스크린샷 2024-01-11 오후 7 38 34" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/da3f3656-2c9d-4d07-947a-3b2bb6d54a10">  

이럴 때 복잡도를 줄일 수 있는 것이 바로 __Decorator!__  

### 구현  
아래와 같이 Decorator를 추상 클래스를 구현한다.  

<img width="630" alt="스크린샷 2024-01-11 오후 7 39 03" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/24a4be3d-6f54-4779-aec4-956e74ecf9ad">  

기본적으로 추상 클래스는 인자로 우리가 사용하려는 객체를 받게 된다.  
해당 객체가 주입되면, 상위 인터페이스에 선언된 메서드를 사용할 수가 있다.  
그럼 추가적인 클래스는, 해당 추상 클래스를 상속받으면 된다!  

<img width="544" alt="스크린샷 2024-01-11 오후 7 51 42" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/2d140d71-4938-4871-9af1-80e9febd33ba">  

위와 같이 추상클래스를 상속받아, ```Fighter```를 주입시킨다.  
또한, 추상클래스가 구현한 ```attack()``` 메서드를 오버라이드 할 수 있다.  
이렇게 여러 개의 클래스를 만들어 놓고 사용한다면 어떻게 될까?  
바로 아래와 같이 사용할 수가 있다.  

<img width="1024" alt="스크린샷 2024-01-11 오후 7 53 05" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/b0774c98-44d9-4b18-bdc6-2ed09191876e">  

위와 같이 생성자를 생성자로 감쌈으로써 여러 기능을 조합할 수가 있다.  
안 좋은 예시처럼 했다면 7개의 클래스를 새로 만들어야 했었다.  
하지만 단 3개의 클래스만으로 7개의 조합을 만들어 낼 수 있는 패턴이 되겠다.  
