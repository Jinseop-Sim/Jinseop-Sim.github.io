---
layout: post
title: "내 프로젝트는 객체 지향적인가? (4)"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
이번에는 다른 프로젝트를 리팩토링 해보려고 한다.  
이 프로젝트는 기능도 몇 개 없고, 크기가 매우 작기 때문에  
리팩토링을 빠르게 진행하고 기능도 추가해보려고 한다.  

### 세션 리팩토링
이전 게시글에서 세션 기능을 추가했었다.  
하지만 아래와 같이 메서드나 코드의 형태로 서비스 군데군데 흩어져 있었다.  

<img width="617" alt="스크린샷 2024-03-28 오후 8 10 17" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/70f85611-00ff-4348-91da-4228c62eef1f">  

<img width="597" alt="스크린샷 2024-03-28 오후 8 09 38" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/e59d6c32-d21f-4818-8047-242dc74dffb6">

위의 코드들은 분명히 중복되는 코드들이다.  
만약 로직을 수정할 일이 생긴다면, 모두 찾아서 고쳐야 한다.  
너무너무 객체 지향적이지 못한 코드라고 볼 수 있다.  
그럼 어떻게 하나의 클래스로 빼낼 수 있을까?  

아래와 같이 ```SessionUtil``` 클래스를 하나 만들어주었다.  

<img width="657" alt="스크린샷 2024-03-28 오후 8 33 25" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/99abe132-d2ed-4ed4-98e5-e5904bba10ee">  

내부에는 ```getCurrentMember()``` 함수가 존재한다.  
해당 함수를 통해, 세션의 유무를 체크하고 동시에 사용자를 반환한다.  
만약 세션이 없다면, 동일하게 ```Exception```을 응답하도록 한다.  
세션이 있다면? 로그인 된 유저로 판단하고, 멤버의 정보를 그대로 반환하도록 한다.  

잘 작동하는 지 직접 확인해보도록 하자.  

#### 잠깐! 발생한 문제  
잠시 문제가 발생했다.  
위의 함수를 보면 ```Member```를 결과로 반환하기 위해 ```Casting```을 하고있다.  
하지만 그 과정에서 에러가 발생했다.  
```Optional```은 ```Member```로 ```Casting```할 수 없다는 오류를 만났다.  

뭐가 문제일까 잠깐 생각해보니, 꽤 당연하게 발생한 오류였다.  
Member은 내가 직접 만든 객체이니, ```Session```에 어떻게 저장될 지 알 수 없다.  
따라서 안전하게 ```Long```이나 ```String```을 값으로 저장하는게 맞다고 판단했다.  
아래와 같이 함수를 변경해주었다.  

<img width="678" alt="스크린샷 2024-03-28 오후 9 38 46" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/d236f468-85c2-4184-9001-29dfebb1faad">  

이제 회원가입을 진행한 뒤, 로그인을 진행해주도록 하자.  
로그인을 하지 않은 경우, 정상적으로 아래와 같이 응답하게 됨을 볼 수 있다.  

<img width="425" alt="스크린샷 2024-03-28 오후 8 25 52" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/6942c269-fc60-438c-a8f0-678375a3721e">  

만약 로그인을 한다면?  
아래와 같이 정상적으로 ```마이페이지``` 기능에 대해 응답을 받을 수 있다.  

<img width="431" alt="스크린샷 2024-03-28 오후 9 46 11" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/fecb7bb7-2cec-4741-b079-bcb33173b3bc">  

추가적으로 정상적으로 사용자의 정보를 받아오는지 확인해보았다.  
사용자를 받아온 뒤, 사용자의 닉네임을 출력하도록 로그를 찍어보았다.  
내가 회원가입한 닉네임이 아래와 같이 잘 나옴을 확인할 수 있다.  

<img width="859" alt="스크린샷 2024-03-28 오후 9 44 31" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/83501cfe-50ee-49be-b92a-0ef3a6c70b61">

이것으로 세션을 불러오는 함수는 리팩토링 끝!  
남은 부분에 리팩토링 해야할 부분이 너무나도 많다.  
차근차근 진행해보도록 하자.  

### 웹툰 서비스 리팩토링
현재 웹툰 서비스의 코드들을 보면 DTO가 정리가 되어있지 않다.  
또, 생성자가 모두 노출되어있어 코드가 길어져있다.  
이를 리팩토링 해보려고 한다.  

#### 웹툰 상세 페이지
예를 들어 아래의 웹툰 상세 페이지 응답 메서드를 보자.  
굉장히 지저분하고 생성자의 매개변수가 모두 노출되어 있다.  

<img width="701" alt="스크린샷 2024-03-28 오후 10 23 17" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/b5d55037-cee0-4cde-9471-a61df43b6946">  

아래와 같이 코드를 조금 깔끔하게(?) 고쳐보았다.  
우선 기능이 조금 어색한거 같아, 로직을 조금 수정하도록 했다.  
로그인 한 경우엔 내가 준 별점으로, 아닌 경우엔 평균 별점을 출력하도록 한다.  

<img width="949" alt="스크린샷 2024-03-28 오후 10 53 06" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/f50b52b3-7f18-4f2b-93b6-1a64e5b4d141">  

또, Dto에 ```정적 팩토리 메서드```를 만들어 매개변수에 필드가 드러나지 않도록 했다.  
또한 메서드가 이름을 갖기 때문에, 역할이 명확해졌다.  

#### 웹툰에 별점 추가하기
별점 추가하기 기능 또한 만만치 않게 지저분하다.  
아래의 사진과 같이 굉장히 지저분한 코드를 볼 수 있다.  

<img width="962" alt="스크린샷 2024-03-28 오후 11 06 47" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/4b8928da-9923-42b4-9965-a0ec4f9a0ab5">  

우선 DTO에 존재하는 ```sendStarLike``` 메서드부터 없애려고 한다.  
DTO에서 ```StarLike```라는 엔티티에 의존하는 것은 좋지 않다고 생각한다.  

<img width="753" alt="스크린샷 2024-03-28 오후 11 07 56" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/fb955f43-e20a-4e09-acd6-d2f2fab0434c">  

그리하여, ```StarLike``` 엔티티 내에 ```toStarLike``` 메서드를 만들었다.  
이렇게 되면, DTO는 더 이상 ```StarLike```에 의존하지 않아도 된다.  
이제 또 다른 메서드 ```postStar()```를 한번 보도록 하자.  

<img width="309" alt="스크린샷 2024-03-28 오후 11 09 28" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/74dd1788-184b-4a35-a381-359d2ec47da9">  

```StarLike``` 내부에서 ```Member```와 ```Webtoon```에 접근하고 있다.  
물론 필드에 존재하는 엔티티이긴 하나, 아무래도 다른 엔티티의 필드를 호출하는 일이다.  
또한, 생각해보니 양방향으로 굳이 ```Stars``` 배열이 있을 필요가 없다고 판단되었다.  
따라서 아래와 같이 아예 제거하는 쪽으로 코드를 변경했다.  

<img width="761" alt="스크린샷 2024-03-28 오후 11 14 56" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/b956e04b-dec0-4796-adac-ffbd896ef57c">  

단, ```Webtoon``` 엔티티에는 ```starCount```를 만들어 두었다.  
사람들이 누를 때 마다 해당 값이 올라가, 평균을 구할 수 있도록 할 것이다.  

### 마이페이지 서비스 리팩토링
마이페이지에는 현재 기본적으로 나의 정보를 출력하도록 되어있다.  
아래와 같이 ```내가 쓴 댓글```, ```닉네임```, ```칭호``` 등을 볼 수 있다.  

<img width="804" alt="스크린샷 2024-03-28 오후 11 24 25" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/e52d08d1-9ca0-4122-ae05-1b1203a661f7">  

하지만, 코드가 굉장히 지저분함을 볼 수 있다.  
쓸데없이 빈 ```List```를 생성하기도 하며, 매개변수가 모두 드러난다.  
아래와 같이 코드를 수정해보았다.  

<img width="803" alt="스크린샷 2024-03-28 오후 11 40 59" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/f67ffddf-b6d5-4561-988a-4f38e04a0295">  

댓글을 불러오는 코드를 ```stream()```을 적용해서 간소화했다.  
또한 매개변수에 필드가 드러나지 않도록, 객체를 집어넣어 주었다.  
