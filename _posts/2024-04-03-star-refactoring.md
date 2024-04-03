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

### 책임의 분리
우선은 ```Service```단에서 책임을 분리해야겠다는 생각이 들었다.  
지금은 ```별점 추가```와 ```별점 수정```이 같은 함수에서 일어나고 있다.  
또한 같은 ```Controller```에 대해서 처리가 되고 있다.  
아래와 같이 구현되었으며, 이는 객체 지향의 ```SRP```에 위반 된다고 생각한다.  

<img width="933" alt="스크린샷 2024-04-03 오후 7 57 25" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/0bbbd42d-ae76-4b61-84a5-d1a92c35d378">  

차라리 ```Controller```를 분리시켜서, 프론트엔드에서 처리를 하는게 맞다는 생각이 들었다.  
프론트엔드에서 이미 별점이 있다면 ```/updateStar```` 경로로 요청을 보내고,  
처음 별점을 매기는 것이라면 ```/pushStar```로 요청을 보내는 식으로 구현할 수 있을 것이다.  
아래와 같이 ```Controller```를 분리시켰다.  

<img width="843" alt="스크린샷 2024-04-03 오후 7 59 53" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/cb6156f7-241b-424f-a629-29c1ca009e1d">  

또한 ```Service``` 레이어도 아래와 같이 함수를 분리시켰다.  
이제 ```Controller```와 ```Service```의 메서드들은 각각 하나의 책임을 가진다.  

<img width="815" alt="스크린샷 2024-04-03 오후 7 59 37" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/6ee4d6b8-534c-4bfd-9f62-48cf7325ad88">  

### 코드 중복 제거
또한 ```Service``` 레이어의 코드 중복을 제거하려고 한다.  
기존에는 아래와 같이 중복되는 코드들이 곳곳에 사용되고 있었다.  

<img width="693" alt="스크린샷 2024-04-03 오후 8 07 46" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/7c86cc50-9f1e-478d-af20-0b8c827c6920">  

```getScore()```과 같은 코드는 한번에 빼서 재사용할 수 있다.  
저런 부분 또한 나는 ```Code smell```이라고 생각하며, 아래와 같이 수정했다.  

<img width="513" alt="스크린샷 2024-04-03 오후 8 12 13" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/ad08423d-71aa-4c25-a66d-5d47c5720e33">   

### 함수 통합
코드 중복 이외에도, ```Entity``` 내부의 함수를 손봐야겠다는 생각이 들었다.  
기존의 ```Webtoon Entity``` 내부 메서드에는 아래와 같은 메서드들이 있다.  

<img width="308" alt="스크린샷 2024-04-03 오후 8 13 33" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/c29e9b11-1209-46ab-b104-ecdb69edacde">  

내가 생각하기에는 ```updateStar```로 충분히 통합할 수 있는 부분이라고 생각된다.  
아래와 같이 통합해보자.  

<img width="329" alt="스크린샷 2024-04-03 오후 8 20 44" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/4798ed78-aa7a-4d92-8f42-7937c5e7b8ae">  

메서드를 위와 같이 통합하니, ```Service``` 로직도 아래와 같아졌다.  
기존에 ```if```문으로 분기하던 로직이 사라지고, 굉장히 깔끔해졌다.  

<img width="505" alt="스크린샷 2024-04-03 오후 8 22 37" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/3c860e0b-9322-4ddb-baf8-bf72cffeedaa">  

또한 ```Member Entity```의 내부에는 아래와 같은 메서드들이 있다.  

<img width="425" alt="스크린샷 2024-04-03 오후 8 14 43" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/fad9a04e-b038-443a-8407-7d74355291b6">  

칭호 시스템이 존재하기 때문에 만들어 놓은 메서드들이다.  
생각해보니 ```updateStar```로 통합하거나, ```1점, 5점``` 2개의 메서드만 있어도 될 것 같다.  
이번에는 ```Member Entity```를 한번 수정해보도록 하자.  

<img width="345" alt="스크린샷 2024-04-03 오후 9 25 37" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/ecadfbd6-2b31-4019-8eab-dc3dfd60f2c8">  

우선은 위와 같이 통합해보았다.  
차라리 ```Member Entity```에 ```if``` 분기를 주는게 낫다고 판단했다.  
칭호에 대한 책임은 ```Member Entity```에 있기 때문이다.  

또한 ```Service``` 로직에서 분기를 없애기 위해 ```Enum```을 사용했다.  
이런식으로 ```Enum```을 사용할 수 있다는 것은 이번에 처음 알게 되었다.  

<img width="547" alt="스크린샷 2024-04-03 오후 9 27 54" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/65286404-d2da-4103-8e3b-45873c66bf58">  

위와 같이 계산 식을 ```Enum``` 클래스에 정의해서 사용하는 것이다.  
```Enum```의 생성자로 들어온 매개변수를 ```Function```으로 받을 수가 있다.  
그럼 ```Function```의 ```apply``` 메서드를 통해 내가 넣은 수식을 실행할 수 있다.  
이제 아래와 같이 ```Service``` 로직이 더 깔끔해지게 된다.  

<img width="844" alt="스크린샷 2024-04-03 오후 9 28 52" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/376edcbe-226f-4ca0-9369-d513ed4004e0">   

분기문들이 싹 사라진 모습을 보니 굉장히 기분이 좋다.  
이제 제대로 동작하는 지 테스트를 해보도록 하자.  

### 테스트 코드
기존에 테스트 코드를 짜놓지 않아서 걱정이 었는데 잘됐다.  
이번 기회에 테스트 코드를 쭉 짜며 테스트에 대해서도 공부하려 한다.  
먼저 아래와 같이 ```별점 기능```에 대한 테스트를 만들었다.  

<img width="784" alt="스크린샷 2024-04-04 오전 1 08 47" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/03d22617-96a5-4119-8365-a544e58addbf">  

따로 ```Mock```객체를 사용해서 실제 동작을 테스트하거나 하지는 않았다.  
단순히 로직이 올바르게 동작하는지만 확인할 수 있는 코드이다.  
```@BeforeAll``` 어노테이션을 통해 미리 ```Entity```들을 생성해 사용한다.  

<img width="635" alt="스크린샷 2024-04-04 오전 1 10 30" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/64690a57-114e-4a2e-8c19-fd58d0f86173">  

테스트 프레임워크인 ```Junit```에서 제공하는 ```Assertion```을 통해 값을 비교한다.  
객체도 비교가 가능한데, 나는 지금 별점 기능을 테스트하니 ```Double``` 값만 비교한다.  
위의 사진과 같이 테스트에 실패할 경우 왜 실패했는지 친절하게 알려준다.  
뭔가 로직이 잘못되었는지, 원하는 대로 값이 나오지 않았다.  

<img width="511" alt="스크린샷 2024-04-04 오전 1 14 29" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/b1ecffe7-78ca-47d7-bfef-306754012a1d">  

곰곰히 생각해보니 ```Source - Target```을 빼는 것이 잘못되었다.  
당연히 내가 바꾸려는 값에서 원래 등록해놓은 값을 빼야 차이가 계산이 된다.  
예를 들어 원래 별점이 ```4```점 이었는데 ```1```점으로 변경하고 싶다면?  
```1 - 4```인 ```-3```을 ```starSum```에 계산해주어야 하는 것이다.  

<img width="606" alt="스크린샷 2024-04-04 오전 1 17 23" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/7b469281-2448-4205-a3a7-55dc2e8ce95d">  

바꿔주니 모든 테스트가 무사히 통과되었다!  
뭔가 더 리팩토링 해야할게 있는지 좀 더 고민해보도록 하자.  
