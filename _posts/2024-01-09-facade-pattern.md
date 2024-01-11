---
layout: post
title: "Facade pattern?"
categories: Tech
tags: [theory]
author:
  - Jinseop Sim
toc: true
---
이번에는 Facade 패턴에 대해서 학습해보자.  

### Facade?
사실 Facade 패턴은 패턴으로 분류하기도 조금 애매하다.  
아래와 같이 간단하게 정의할 수 있을 것 같다.  

> 다양한 클래스의 메서드를 모은 종합 선물 세트

### 사용 이유
만약 각기 다른 클래스에서 메서드를 불러와 연속 동작을 해야 한다면?  
아래의 사진과 같이 사용자 입장에서는 모든 메서드에 대해 알고 사용해야 한다.  
클래스의 종류가 늘어나면 늘어날 수록 매우 불편할 것이다.  
그럴 때 Facade 패턴이 매우 도움이 된다!  

<img width="648" alt="스크린샷 2024-01-10 오전 10 46 46" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/010ae38e-ae05-4a6d-a590-5fd087712d65">  

사용자가 주로 사용하는 클래스, 메서드들의 연속동작을 하나로 묶는다.  
다시 말해, Facade 클래스를 만들어 한 세트로 패키징을 하는 것이다.  

각기 다른 클래스에서 온 수많은 메서드들은 Facade, 즉 외벽 뒤에 가려  
사용하는 사용자는 그 내용을 알 필요 없이 그냥 한 번에 사용할 수 있다.   

### 구현
Facade 패턴은 아래와 같이 구현할 수 있다.  

<img width="483" alt="스크린샷 2024-01-10 오전 10 48 39" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/667d6593-bebc-449a-9acf-0c83139c9419">  

위에서 보았던 일련의 동작들을 하나의 메서드로 만들어 버린다.  
이렇게 구현하면 사용자는 해당 함수 하나만 알면 된다!  
객체 지향 개발자라면 많이 구현해 보았을 법한 방식의 코드이다.  