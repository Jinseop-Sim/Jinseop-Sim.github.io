---
layout: post
title: "Factory & Abstract Factory"
categories: Tech
tags: [theory]
author:
  - Jinseop Sim
toc: true
---
이번에 알아볼 패턴은 __Factory__ 와 __Abstract Factory__ 이다.  
둘은 결국은 같은 구조이기 때문에 한번에 알아보도록 하자.  

### Factory?
기본적으로 Factory method 패턴이란 어떤 패턴일까?  
아래와 같이 간단하게 정의할 수 있을 것 같다.  

> 다른 개발자가 편하게 클래스를 생성할 수 있도록 공장을 만드는 것!

아마 간단한 정의만 봐서는 이해가 안될 것이다.  
아래로 조금 더 자세하게 알아보도록 하자.  

### 사용 이유
다른 개발자가 편하게 클래스를 생성할 수 있다는 게 어떤 말일까?  
예를 들어, 아래와 같이 여러 개의 클래스가 존재한다고 생각해보자.  
이것을 그대로 다른 개발자에게 넘겨서 개발하게 된다면 어떻게 될까?  
다른 개발자의 입장에서, 클래스를 모두 알아야 하니 번거로울 수가 있다.  

<img width="701" alt="스크린샷 2024-01-11 오후 9 34 46" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/18c781d6-ce6e-41f8-8ea3-bbd38da7fa0b">  

해당 생성자들이 변경되게 된다면 사용처에서도 모두 변경해야 할 것이다.  
즉, ```OCP```를 위반하는 동시에 변경에 대한 부담이 클 것이다.  
이를 해결할 수 있는 패턴이 바로 __Factory Method!__

### 구현
아래와 같이 ```ShoeFactory```라는 공장을 하나 만들어보자.  

<img width="738" alt="스크린샷 2024-01-11 오후 9 44 42" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/12924792-c725-4506-9dba-537af5e1a8a9">

그렇게 되면 결국 넘겨받은 개발자는 ```Shoe``` 클래스를 알 필요가 없다.  
공장을 사용하는 방법만 알고 있으면, 별다른 변경 없이 객체를 생성할 수 있다!  

<img width="1129" alt="스크린샷 2024-01-11 오후 9 46 41" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/8ff472b1-6bb4-48d3-bc9b-5bd4726c5b98">  

위와 같이 ```ShoeFactory```만으로 원하는 객체를 생성함을 볼 수 있다.  

### 응용
한 가지 예시를 더 들어보자면, Decorator 패턴과 결합할 수 있다.  
Decorator 패턴에 구현했던 ```XWingFighter```를 다시 보자.  

개발자가 갑자기 바뀌는 경우라면, Decorator 클래스들 또한 부담이 된다.  
해당 클래스들을 모두 알고 있어야 객체 생성이 가능하기 때문이다.  
하지만 아래와 같이 ```FighterFactory```를 추가하게 된다면?  

<img width="732" alt="스크린샷 2024-01-11 오후 9 49 03" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/52c2d49f-6690-454e-aecd-7baedaa3d8ff">  

조건에 따라 어떤 무기를 부착할지 사용법만 안다면?  
클래스의 구조나 생성자를 굳이 알지 않아도 객체 생성이 가능하다!  

<img width="1066" alt="스크린샷 2024-01-11 오후 9 49 52" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/024d33f1-fbe1-4c7e-a620-81e6b8cda294">  

결국 위와 같이 ```FighterFactory```만으로 객체를 생성했다!  

### Abstract Factory
추상 팩토리 패턴은 보다 더 추상화 된 패턴이다.  
공장의 종류가 늘어난다는 말로 이해해도 좋다.  

### 구현
예를 들어, 위의 예시였던 신발 공장을 한번 생각해보자.  
공장을 확장해서 중국 지부, 한국 지부를 따로 관리해야 한다.  
개발자 입장에서 어떻게 편하게 공장을 관리할 수 있을까?  
바로 아래와 같이 interface를 적극 이용하도록 한다!  

<img width="756" alt="스크린샷 2024-01-11 오후 10 01 30" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/8856a52a-e8e2-4484-a8c3-599772656670">  

추상화 된 ```Shoe``` 클래스를 생산하는 공장을,  
interface를 통하여 한 번 더 추상화 해주는 것을 볼 수 있다.  
또한, 원래의 ```Shoe``` 클래스도 아래와 같이 변경을 해주자.  
중국 지부에서 관리할 제품의 클래스를 __확장__ 하는 것이다.  

<img width="901" alt="스크린샷 2024-01-11 오후 10 02 45" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/792ad47c-4f9a-4feb-b368-bbc166ef16a9">

추상 팩토리 메서드를 적용하는 과정에서 __변경된__ 부분은 없다.  
오직 중국 공장에 대한 클래스들을 확장해주었을 뿐이다.  
넘겨받은 개발자는 공장들의 사용법만 알면 동일하게 객체 생성이 가능하다.  
굉장히 효율적인 패턴이다!  
