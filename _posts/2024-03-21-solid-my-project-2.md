---
layout: post
title: "내 프로젝트는 객체 지향적인가? (2)"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
지난 글을 쓰며, 곰곰히 생각을 해보았다.  
```Service```와 ```Repository```에서 추상화 시킨 ```Contents```를 사용하려면?  
반드시 ```Contents <-> 자식```간의 캐스팅이 자유로워야 한다.  
```interface```나 ```abstract class```를 이용해서 추상화를 시키기는 힘들다고 생각된다.  

만약 완전히 통합해서 ```Service```와 ```Repository```를 사용하고 싶다면,  
애초에 ```Entity```를 하나의 통합 클래스로 만들었어야 할 것 같다.  
현재 내 프로젝트는 아래와 같은 상속 구조로 구성되어 있다.  

<img width="353" alt="스크린샷 2024-03-21 오후 7 50 54" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/190e09c1-0ca2-4c48-97f6-0ed8c45fdb8d">  

따라서 만약, ```Repository```에서 ```List<Contents>```를 반환한다면?  
DB에서 가져온 값들 중, ```Contents``` 클래스의 필드만 가지게 되는 것이다.  
따라서 우리는, 애초에 ```Repository```에서 특정 클래스를 반환해야만 한다.  

이렇게 꼬인 상황에, 하나의 ```Repository```로 모든 클래스에 맞추어 반환할 수는 없을까?  

### Java Generic의 사용
곰곰히 생각해보니, Java에는 ```Generic```이라는 개념이 존재한다.  
```ArrayList```나 ```HashMap``` 같은 자료구조를 쓸 때를 생각해보자.  
```ArrayList<Integer> arr = new ArrayList<>();```와 같이 선언할 것이다.  
이 때, 바로 ```<>``` 안에 적어넣는 타입을 우리는 ```Gerneric```이라고 한다!  

<img width="631" alt="스크린샷 2024-03-21 오후 9 08 20" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/61ba363d-6db0-48a9-b964-e891a5ff28c1">  

위의 사진과 같이, 특정 Class가 아닌 ```<E>```로 기재된 것을 볼 수 있다.  
간단하게 말해, 클래스 내부가 아닌 사용자에 의해 내용물의 클래스가 결정된다는 것이다.  
보통 잘못된 타입이 들어오는 것을 __Compile Time__ 에 잡아내기 위해 사용한다.  
또한 유사한 기능들을 제공하는 템플릿을 만들 때, 재사용성을 높이기 위해 사용하기도 한다.  

우리는 지금 재사용성을 높여야 하는 상황이니, 써볼만 한 것 같다!  
또 ```JPARepository```를 생각해보면, ```Generic```이 사용되고 있다.  
