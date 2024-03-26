---
layout: post
title: "내 프로젝트는 객체 지향적인가? (3)"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
### 게시글 별 응답을 분리해야 할까?
저번 글에서 잠깐 언급했던 고민이 아직도 고민이다.  
프론트엔드에서 데이터를 요청해서 내가 반환을 해준다고 생각해보자.  
만약 지금 통합한 상태의 ```DTO```로 응답을 한다면?  

```null```로 들어간 필요 없는 값까지 모두 포함해서 응답이 될 것이다.  
그럼 프론트엔드 입장에서는 아래와 같이 너무 많은 필드가 한번에 넘어갈 것이다.  

<img width="454" alt="스크린샷 2024-03-25 오후 9 27 58" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/0e869472-2a1e-436b-99a6-0e392528e270">

사용되지 않는 필드까지 한 번에 넘어가면, 프론트의 입장에서 헷갈릴 것 같다.  
이를 방지하기 위해서 기존의 ```DTO```로 바꾸어 넘겨줄 수 있을 것이다.  
아래와 같이 ```Controller```에서 변경한다면 어떨까?  

<img width="950" alt="스크린샷 2024-03-25 오후 9 05 49" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/597ccc51-bbe0-4d4c-b5bb-72571d748f2f">  

<img width="916" alt="스크린샷 2024-03-25 오후 9 36 04" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/6de4b4ea-6030-4d39-816d-b721f5b7a037">  

그럼 모든 필드가 전송되던 이전과는 달리, 아래와 같이 응답이 전송된다.  
필요한 필드만 모아놓은 ```ResponseDTO```에 맞추어진 결과를 볼 수 있다.  

<img width="413" alt="스크린샷 2024-03-25 오후 9 44 50" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/d099670c-a73e-4e5d-8eed-b05e9e93d289">  

훨씬 깔끔하게 응답이 나가는 모습을 볼 수 있다.  
이제 생각해야 할 것은 과연 이 코드는 객체 지향적일까?!  

#### 객체 지향적인가?
- SRP : 먼저 모든 Controller는 하나의 책임만을 가지고 있다.
  - 또한 모든 변경이 발생할 때는, 하나의 이유만으로 변경하면 된다.
- OCP : 만약 게시글에 대한 내용을 수정해야 하는 경우에는?
  - ```dto```나 ```Entity```만 수정하면 된다.
  - ```Controller```의 코드는 변경에 대해 닫혀있다!

꽤 객체 지향적으로 짜여진 코드라고 생각한다.  

#### 그럼 게시글을 게시하는 경우는?
게시글을 게시하는 경우에는, ```ContentsRequestDTO```로 통합하고 있다.  
이 경우엔 필요한 필드만 입력하면 나머지는 자동으로 ```null```로 채워진다.  
따라서 따로 고려해야 할 사항은 없는 것 같다.  

#### 추가 리팩토링 가능성
현재 ```DTO```를 변환하는 과정에 ```new``` 키워드가 사용되어 있다.  
이 부분에 ```Static factory method```를 적용하면 어떨까? 생각해보았다.  

먼저 해당 패턴은 왜 사용되는가?  
- 이름을 붙여 생성자의 역할을 명확히 수 있다.
- 매개변수가 매우 많은 경우 매개변수에 따른 용도를 정리할 수 있다.
- 싱글톤 패턴을 적용하여, 객체의 중복 생성을 막을 수 있다.

보통은 매개변수가 많고, 매개변수에 따라 생성되는 객체가 달라지는 경우에 사용한다.  
또한 ```Entity```에서 ```DTO```로 필드 값을 옮길 때 사용되기도 한다.  
```of```나 ```from```을 사용해, 생성자를 직접 부르지 않고 필드 값을 옮기곤 하는데,  
이는 ```Constructor(field1, field2,..)```와 같이 필드값이 노출되는 것을 막기 위함이다.  

나는 ```Entity -> DTO```로 옮기는 과정을 아래와 같이 구현했다.  
```Java stream```을 이용해서, ```DTO``` 생성자로 매핑해준 것이다.  

<img width="818" alt="스크린샷 2024-03-26 오전 12 44 04" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/9a84e8f8-47bf-407f-a368-b33d22a4be0e">  

반대의 경우는 아래와 같이 생성자를 사용하고 있다.  
크게 드러나는 필드 값은 없기 때문에 그대로 사용하고 있다.  

<img width="929" alt="스크린샷 2024-03-26 오전 12 46 58" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/cfef756a-d31a-4cb7-a903-9961a325bb90">  

아직은 크게 필요한 부분이 없는 것 같다.  

### 유효성 검증
기존에 하지 않았던 유효성 검증을 구현해보려고 한다.  
기존에는 프론트엔드 단에서 검증을 했기 때문에, 따로 넣지 않았다.  
하지만 들어온 값에 대해서 확실히 검증하는 것이 좋다는 생각이 들었다.  

<img width="592" alt="스크린샷 2024-03-26 오전 1 20 05" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/184edd98-8a5a-4a6c-966e-72209758d137">  

Springboot에는 기본적으로 위와 같은 ```Validation``` 기능을 제공한다.  
단순히 ```Annotation```만 붙여주면, 알아서 검증을 해준다.  
이런 부분이 정말 Springboot의 편의성 면에서의 강력함이 아닌가 싶다.  

이 부분에서 잠깐 고민이 생겼다.  
```Validation```을 진행해야 할 ```Layer```는 어디일까?  
```Entity?``` ```DTO?``` 어디에서 검증을 해야할까?  
의문이 생겨 다른 사람들의 의견을 조금 참고해 보았다.  

```StackOverFlow```를 조금 뒤져본 결과, 아래와 같은 답변들이 있었다.  

<img width="661" alt="스크린샷 2024-03-26 오후 8 28 26" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/8d50a8a7-2cef-4038-8b76-f4acf0d6f2f2">  
<img width="666" alt="스크린샷 2024-03-26 오후 8 36 52" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/7ee6e857-7e60-4416-b2a7-6e6f9e33d0e0">  

대부분의 개발자들은 모든 ```Layer```에서의 유효성 검증이 필요하다고 한다.  
```Entity```와 ```DTO```에서 검증을 진행하고,  
심지어는 ```Service Layer```에서 또한 검증을 진행함이 필요하다고 한다.  
```Controller```에서 ```Service```로 정상 값이 넘어옴을 가정함은 좋지 않다고 한다.  

그래서 나는 우선 ```Entity```와 ```DTO```에 모두 ```Validation```을 달았다.  

<img width="744" alt="스크린샷 2024-03-26 오후 8 41 30" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/baaaa1d5-2143-466e-a1e5-26ba1cfb3622">  

이후 ```Controller```에 ```@Valid``` Annotation을 부착했다.  
그 결과, 아래와 같은 로그가 서버에 뜨게 된다.  
해당 오류는 ```400```으로 처리 되기 때문에, 

<img width="869" alt="스크린샷 2024-03-26 오후 8 50 20" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/eeee040b-bb8f-4313-ac2f-6a04027a4fdc">
