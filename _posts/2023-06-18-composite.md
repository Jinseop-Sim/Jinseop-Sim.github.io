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
