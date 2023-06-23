---
layout: post
title: "Vue의 Composite API"
categories: frontend
tags: [nodejs, vuejs]
author:
  - Jinseop Sim
toc: true
---
이전까지는 vue의 문법에 대해서 간단하게 학습을 했었다.  
지금부터는 조금 더 개념적인 부분을 문법과 함께 학습하려 한다.  
본격적으로 __Vue__ 를 알아가는 부분이라고 봐도 될 것 같다.  

## Options API
먼저, 내가 이 때 까지 사용했던 방식은 ```Options API```라는 방식이다.  

```data```에 변수를 선언하고, ```methods```에 함수를 선언해 사용한다.  
이외에도 생명주기를 담당하는 ```mounted, updated, beforeUnmount...``` 라던가,  
계산된 값을 반환하는 ```computed```, 업데이트를 탐지하는 ```watch``` 등의  
다양한 객체 형태의 모듈을 선언할 수 있는 방식이다.  
```props``` 라는 선언도 존재하는데, 이는 다른 포스트에서 다루도록 하겠다.  

해당 방식은 역할과 책임에 따라 분류는 하였으나, 만약 컴포넌트 수가 늘어난다면?  
앱의 규모가 매우 커지면, 분명히 왔다갔다하며 번거롭게 코드를 읽어야 하게 될 것이다.  
그래서 Vue에서는 __Composition API__ 라는 방식을 권장하며, 제공한다!  

## Composition API
물론, Composition API가 꼭 필수로 사용해야 하는 방식은 아니다.  
하지만 추후에 Vue를 실제로 현업에서 사용해야 하는 때가 온다면,  
분명히 Options API보다는 Composition API를 권장하고 사용하게 될 것이다.  

### ```Setup()```
기본적으로 Composition API는 ```setup()``` 메서드를 통해 사용한다.  
Options API와는 시작하는 부분의 형태부터 다르다는 것을 알 수 있다.  
아래와 같이 변수와 함수를 선언한 뒤, ```return```하여 ```template```에서 사용할 수 있다.  
![image](https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/f03e67db-fd2d-42d2-9f4f-34dc24ac8404)  
![image](https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/7f40a88c-074a-430f-a366-bd63e95bd031)  

> ```ref```는 __반응형 변수__ 라는 것인데, 따로 포스팅을 하도록 하겠다.  
> 물론, 반응형 변수가 아닌 그냥 변수로 사용해도 무방하다.  

그 결과 아래와 같이 간단한 테스트 메시지가 잘 출력됨을 확인할 수 있다.  
![image](https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/75c63215-6d77-44cb-839c-04eb41ff6f50)  

### ```<script setup>```
위에서 구현했던 ```setup()``` 메서드는 좀 더 간결하게 표현할 수 있다.  
아래와 같이 ```<script setup>``` 태그를 이용하는 경우, 더 간결하게 표현이 가능하다.  
![image](https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/76a882cc-ac95-46a8-b8af-909b0725f360)  

가장 큰 특징으로는 ```return```문이 사라졌음을 확인할 수 있고,  
따로 ```setup()``` 메서드를 선언하지 않아도 자체적으로 변수와 함수가 선언된다는 점이다.  
