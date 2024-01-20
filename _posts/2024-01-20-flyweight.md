---
layout: post
title: "Flyweight Pattern?"
categories: Tech
tags: [theory]
author:
  - Jinseop Sim
toc: true
---
> __해당 글은 Tstory 블로그 Dev inpa에 출처를 두고 있습니다.__

이번 패턴은 Flyweight 패턴에 관한 글이다.  
나는 살면서 처음 들어보는 생소한 패턴이다.  

### Flyweight?
패턴의 이름을 말 그대로 직역하면 경량 패턴이다.  
정의도 이름을 따라가는데, 아래와 같이 간단하게 정의할 수 있겠다.  

> 객체의 생성을 줄여 메모리를 경량화하기 위한 패턴!

말 그대로 객체를 경량화 시킬 수 있는 패턴이다.  
아래로 예시를 보며 좀 더 자세하게 공부해보도록 하자.  

### 사용 이유
예를 들어 마인크래프트 게임을 한 번 생각해보자.  
게임 내에서 어떤 필드가 주어지고, 해당 필드에 나무가 생성이 된다.  
이 때 나무들은 각자 특징을 가질 수 있으며, 모두 하나의 객체로 취급된다.  

아래와 같이 나무 객체, 지형 객체, 그리고 전체 메모리가 있다고 가정하자.  
각각의 객체를 자바 코드로 구현한 것이다.  

<img width="736" alt="스크린샷 2024-01-20 오후 6 36 18" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/42d4a785-ecb8-453d-a989-ec7376a99f31">  
<img width="851" alt="스크린샷 2024-01-20 오후 6 36 35" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/e25a2319-0529-4ac3-80fe-36e653da92bd">  
<img width="570" alt="스크린샷 2024-01-20 오후 6 36 49" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/12419fe7-7ce9-431a-af2c-917b1c869bb0">  

이 코드들 기반으로 각 특징 별 나무를 5그루씩 심어보도록 하겠다.  
아래와 같이 메인 함수에서 나무를 심는 시뮬레이션이 가능하다.  

<img width="929" alt="스크린샷 2024-01-20 오후 6 38 04" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/3834590d-0922-49e8-aa29-d1aafbbac2c9">  

미리 생성된 ```10000``` 크기의 지형에 나무를 5그루씩 심었다.  
나무를 심을 때 마다 객체가 생성되므로, 총 15그루의 나무를 심었다.  
따라서 총 사용된 메모리는 ```100 * 15 = 1500MB```가 된다.  

사실 생각해보면, 나무들의 특성인 ```mesh, texture```은 어떤가?  
같은 특징의 나무라면 분명히 동일한 ```mesh, texture```를 가지게 된다.  
즉, 같은 특징의 나무는 위치만 다르지 동일한 특성을 갖는다는 것이다!  
그럼 사실상 우리는 메모리를 낭비하고 있다고 볼 수 있다.  

이럴 때 사용할 수 있는 패턴이 바로 __Flyweight 패턴!__  

### 구현
객체들 간에 중복되는 특징인 ```mesh, texture```을 하나의 클래스로 빼낸다.  
이 클래스를 우리는 ```Flyweight``` 클래스라고 칭하겠다.  
또한, 공통된 부분은 변경될 일이 없으므로 ```final```로 선언한다.  

<img width="566" alt="스크린샷 2024-01-20 오후 11 57 03" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/ce436d49-0620-411e-a220-10f64a35d1d0">  

공통된 부분을 빼내었으면 이제 해당 클래스를 생산할 공장이 필요하다.  
그냥 해당 객체를 생성하는 것이 아닌 유일한 객체를 생성하기 위함이다.  
```정적 팩토리 메서드```를 이용해서 유일한 객체를 반환할 수 있다.  
