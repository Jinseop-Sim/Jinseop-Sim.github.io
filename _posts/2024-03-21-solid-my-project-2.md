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

그러나 또 문제점이 발생했다.  
이런 방식으로 구조를 바꾸는 경우엔, 아래와 같이 ```Repo```를 사용하게 된다.  
<img width="877" alt="스크린샷 2024-03-21 오후 9 46 57" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/147e39cf-1fb4-40cc-9d17-dd8a9cfdff0f">  

처음엔 결과가 제대로 나오길래, 좋아했지만.. 생각해보니 잘못된 구조이다.  
```JPARepository```의 경우를 대입해서 생각해보면,  
지금 ```jpaRepository.findAll()```을 해서 받아오는 격이 된다.  
즉, 어차피 ```groupBuyingRepository.findAll()```을 해야하는 상황이다.  

일련의 고민과 과정들을 거치고 나니.. 하나 깨닫게 된 것이있다.  
바로 __처음에 엔티티를 올바르게 잘 설계할 것!!__  
계속 고민해봤지만, 나는 이제 아래의 두 선택지 중 선택해야 한다.  
1. 세 종류의 게시글을 하나의 Class로 합친다.
2. 상속 구조를 풀고 3개의 게시글을 애초에 다른 클래스로 구분한다.

전자의 경우에는, 전체적으로 코드를 다시 짜야한다.  
또한 전자를 선택하는 경우, 아래와 같이 클래스가 매우 커지게 된다.  

<img width="978" alt="스크린샷 2024-03-21 오후 10 02 54" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/8dd41e5d-2bae-4e00-af8c-5032d384e3b6">  

위와 같이 추가적으로 붙는 필드가 너무나도 많아지게 된다.  
후자의 경우는 상속 관계만 풀고, 모든 구조를 원래대로 사용하면 된다.  

하지만, 지금은 공부를 하는 단계이니 위험을 무릅쓰고 전자를 선택하도록 하겠다.  

### 결국 처음으로
전자를 선택하여 게시글 3개를 하나로 통합한 뒤, 리팩토링을 진행하도록 하자.  
또한, ```좋아요```를 누른 글과 ```참여```한 글 또한 리팩토링이 필요하다.  

<img width="213" alt="스크린샷 2024-03-21 오후 10 04 33" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/b6762a7d-c1d1-4bd8-b900-faf11a18b772">  

기존에는 위와 같이 모두 분리되어있는 모습을 볼 수 있다...  
아래와 같이 통합된 ```유저 참여, 좋아요``` 클래스를 생성해 주었다.  
만약을 대비해 기존 클래스는 일단 살려두고, 통합된 ```Serv, Repo```를 만들었다.  

<img width="261" alt="스크린샷 2024-03-21 오후 10 40 06" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/cdfc5146-1ab1-4dfc-85d7-40517a1def0b">  

<img width="241" alt="스크린샷 2024-03-21 오후 10 41 07" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/9634a87f-868f-4a63-a70a-1a0a52f3367c">  

이제부터 ```게시글, 참여, 좋아요```와 관련된 클래스를 모두 리팩토링 할 것이다.  
프로젝트의 크기가 크지 않아 시간이 많이 걸리지는 않을 것이라고 생각한다.  

### 클래스 대통합
이틀 정도 게시글과 관련된 모든 코드를 리팩토링했다.  
그 결과, 중복되는 코드를 많이 제거할 수 있었고 좀 더 깔끔하게 정리할 수 있었다.  

#### 게시글 클래스
우선 게시글 클래스는 모두 ```Contents```라는 접두사로 통합되었다.  
아래의 사진들을 통해, ```Serv, Repo``` 모두 통합되었음을 확인할 수 있다.  

<img width="930" alt="스크린샷 2024-03-24 오후 3 48 29" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/c9f30488-d4ec-4096-9b18-cae79f3e72d7">  

<img width="1011" alt="스크린샷 2024-03-24 오후 3 48 47" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/5a0a122a-da87-4ead-a1e3-dd07813c6d7b">  

3개로 분리되어있던 ```Serv, Repo``` 클래스를 모두 제거하였다.  
자연스럽게 하나의 클래스로 코드가 모이며, 중복 코드는 제거된 것이다.  
또한 게시글을 요청, 반환하는 ```DTO```들이 클래스와 덩달아 비대해지게 되었다.  

<img width="369" alt="스크린샷 2024-03-24 오후 3 51 13" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/350f030b-821f-4b95-ac79-f43315a3cb2d">  

하지만, 감당 가능할 정도의 크기라고 생각한다.  
중복되는 코드가 많은 것 보단, 클래스를 우고 주석을 다는 것이 낫다는 생각이 들었다.  

#### 좋아요 클래스
좋아요 기능과 관련된 ```Entity``` 또한 하나로 통합되었다.  
기존에는 언급했듯, 사용되는 게시물 별로 분리되어 있었다.  
하지만 아래와 같이 깔끔하게 하나로 통합하게 되었다.  
자연스레, ```Repository``` 또한 하나로 통합되어 깔끔해졌다.  

<img width="370" alt="스크린샷 2024-03-24 오후 3 53 13" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/2c35cff5-7980-4c66-9340-a4fd8bd46177">  

아래의 두 사진을 통해 기존 ```Repo``` 클래스와 비교할 수 있다.  

<img width="876" alt="스크린샷 2024-03-24 오후 3 57 16" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/05f48009-3370-4515-889b-b8a20f453d7d">  

<img width="960" alt="스크린샷 2024-03-24 오후 4 00 00" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/3fd116c1-2fe5-48be-a66c-c53aa5a65bda">  

기존의 코드는 게시글 종류 별로 모두 함수가 따로 작성되었다.  
하지만 클래스를 통합하며, 함수의 수가 절반으로 줄게 되었다.  
훨씬 깔끔해졌다!  


#### 참여 클래스
참여 기능과 관련된 ```Entity```도 통합되었다.  
좋아요와 마찬가지로 아래와 같이 훨씬 깔끔하게 통합되었다.  

<img width="405" alt="스크린샷 2024-03-24 오후 3 53 31" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/a1421d88-d61b-4c9d-81ad-a99f574d3316">  

<img width="917" alt="스크린샷 2024-03-24 오후 3 57 53" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/aa8e9d4b-58af-4286-90b5-13c1a5d6bbb1">

#### 마이페이지
이 기능이 가장 크게 변화가 있었다고 생각한다.  
기존에는 아래와 같이 굉장히 코드가 지저분했다.  

<img width="637" alt="스크린샷 2024-03-24 오후 4 04 34" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/ebcb0e16-d25e-4029-93a1-f27a17adc63c">  

모든 게시글 별로 따로따로 조회를 해야했기 때문에, 너무 지저분했다.  
언젠가는 반드시 리팩토링 하겠다고 생각했었는데, 마침 이번에 하게 되었다.  
아래와 같이 훨씬 깔끔해진 코드를 확인할 수 있다.  

<img width="786" alt="스크린샷 2024-03-24 오후 4 06 36" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/0e051136-4e63-4a61-925b-30270614f626">  

1차적으로 클래스 통합에 대한 리팩토링은 끝난 것 같다.  
이제 사소한 부분을 더 깔끔하게 하고, 기능을 교체하려고 한다.  
혹은 더 할 수 있다면 기능을 더해보고 싶기도 하다!  
