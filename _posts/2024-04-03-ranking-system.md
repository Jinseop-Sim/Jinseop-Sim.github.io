---
layout: post
title: "랭킹 서비스 개발 (w. Redis)"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
프로젝트에 어떤 기능이 좀 더 필요할까 고민해보니,  
문득 웹툰 별로 조회수가 필요할 것 같다는 생각이 들었다.  
조회수를 생각하다보니, 조회수에 따른 랭킹도 알 수 있으면 좋을 것 같았다.  

그래서, 이번에는 조회수와 랭킹 시스템을 한 번 만들어보도록 할 것이다!  

### 조회수
웹툰 별로 조회수를 어떻게 저장하고 불러올 수 있을까?  

간단하게 생각했을 때, ```DB```에 컬럼을 만들 수 있을 것이다.  
```DB```에 새로운 ```views``` 컬럼을 만들어 저장을 하는 것이다.  
하지만, ```DB```에 저장하는 것이 과연 효율적일까?  

조회수가 올라가고, 조회수를 조회하는 시나리오를 생각해보자.  
사용자가 웹툰 상세 페이지를 조회하면, 조회수가 올라가야 한다.  
아래와 같이 도식화 할 수 있을 것 같다.  

<img width="422" alt="스크린샷 2024-04-03 오후 6 01 34" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/47fe6457-8646-4649-9ca0-242b2f6f42cf">  

Increment 동작이 수행된다는 것은, ```UPDATE``` 쿼리가 날아간다는 것이다.  
즉, 사용자가 상세 페이지를 조회할 때마다 ```UPDATE``` 쿼리가 날아가게 된다.  
그럼 매우 많은 사용자가 서비스를 사용하게 된다면?  
이는 분명히 성능 저하를 초래할 수 밖에 없을 것이다.  

또한, 랭킹 시스템을 적용하기 위해서는 매번 DB에 가서 조회수를 확인해야 할 것이다.  
웹툰들의 조회수를 확인한 뒤, 상위 랭킹의 웹툰을 추려 가공하는 작업을 반복해야 한다.  
그 과정에서 매번 조회 쿼리가 날아가게 될 것이고, 이 또한 성능 저하의 위험이 있다.  
따라서 나는 다른 저장소를 사용해 조회수를 적용해보려고 한다.  

바로 In-Memory DB인 Redis!  

### 왜 Redis?
Spring에서 사용되는 In-Memory DB는 보통 두가지를 꼽는다.  
바로 ```Memcached```와 ```Redis```이다.  
굳이 ```Redis```를 내가 사용하고자 하는 이유는 자료구조의 다양성 때문이다.  
물론 ```Redis```는 ```Pub / Sub``` 기능이 있는 등 더 다양한 기능을 지원하기도 한다.  

In-Memory DB의 장점은, 디스크에 접근하지 않는다는 점이다.  
DB에 접근하는 것은 디스크에 접근하는 것과 동일한 동작이라 비용이 크다.  
하지만 ```Redis```나 ```Memcached```는 메모리에 곧바로 쓰기 때문에 비용이 적다.  
즉, 조회 및 삽입 시간이 매우 빠르다!  

### Sorted Set in Redis
우선 랭킹 시스템을 적용하려면, 정렬이 자동으로 되는 자료구조가 필요하다.  
마치 ```C++```의 ```Set, Map```과 같이 말이다.  
그런 점에서 ```Redis```에서 지원하는 ```Sorted Set```을 사용하면 될 것 같았다.  

아래와 같이 Spring의 Redis에서는 ```ZSet```에 대한 메서드를 제공한다.  
미리 선언해놓은 ```RedisTemplate```을 통해서 ```ZSet```을 다룰 수 있다.  

<img width="608" alt="스크린샷 2024-04-05 오전 12 15 22" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/63a53ae8-2163-4989-a80c-24802c473021">  

위와 같이 ```opsForZSet().incrementScore()```를 사용하면, 값이 삽입된다.  
```Sorted Set```은 ```KEY```라는 이름으로, ```Title```을 ```Key```로 생성된다.  
또한 ```incrementScore()```을 통해 올린 ```Score```로 값을 자동으로 정렬한다.  

<img width="722" alt="스크린샷 2024-04-05 오전 12 18 20" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/af757476-9939-463c-b4af-6f2ad15cac4f">  

위와 같이 ```reverseRangeWithScores()``` 메서드를 통해 ```Set```을 조회할 수 있다.  
```<ZsetOperations.TypedTuple<Object>>```라는 형식의 결과가 조회된다.  
해당 결과를 ```Stream```을 통해 ```DTO```로 매핑해서 이용할 수 있을 것이다.  

### 실제 적용
어느 시점에서 조회수가 올라야할까?  
당연히 특정 사용자가 웹툰의 상세 페이지를 조회했을 때, 올라가야할 것이다.  
또한 로그인 된 사용자에 대해서만 조회수를 올릴 수 있도록 해야한다.  

<img width="512" alt="스크린샷 2024-04-05 오전 12 24 35" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/b4ecbd77-f834-4eb3-8c05-a487d2d6367a">  

위와 같이 사용자가 로그인했을 때의 로직에 ```incrementScore()``` 함수를 넣어주었다.  
이제 보니 좀 더 함수를 리팩토링 해야할 것 같기도 하다.  
아직 지저분한 것 같다.  

그럼 이제 오른 조회수에 대해서 5위까지의 웹툰을 조회해야 한다.  
내 생각에는 우선 웹툰 목록페이지에서 해당 랭킹을 볼 수 있으면 좋을 것 같다.  
프론트엔드에서 처리하기 쉽게 아래와 같이 우선 ```Dto```로 감싸주었다.  

<img width="826" alt="스크린샷 2024-04-05 오전 12 44 21" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/f04369ed-8aeb-4169-b047-23742e5fc6f4">  

이제 이 함수를 웹툰 목록페이지에 이식하기 전에, 잠시 생각해보자.  
만약 ```Sorted Set``` 내부에 아무것도 없다면 어떻게 될까?  
```NullPointException```이 발생하게 되면, 서버가 중단되지 않을까?  
테스트를 위해, 테스트 코드를 작성했고 우선 ```DTO```로는 잘 감싸짐을 확인했다.  

<img width="862" alt="스크린샷 2024-04-05 오전 12 54 13" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/38601333-a34f-4914-a2f4-d04004ff8649">  

그럼 이제 강제로 ```flushall``` 명령어를 통해 ```Set```을 비워보자.  
비운 뒤, 다시 한번 테스트 코드를 돌려서 ```NPE```가 발생하는 지 확인해보자.  

<img width="865" alt="스크린샷 2024-04-05 오전 12 56 15" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/bdfc2948-91cb-4e03-a840-65348dc78617">   

```size```에 대한 ```Assertion```을 통해 배열이 비었음을 확인할 수 있었다.  
```TypedTuple```을 ```DTO```로 변환하는 과정은 무사히 수행할 수 있는 것 같다.  
이제 실제로 서비스에 적용을 한 번 해보도록 하자.  

<img width="827" alt="스크린샷 2024-04-05 오전 1 09 14" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/1bdd964b-7736-45bc-9b07-6c7498386edb">  

위와 같이 ```Controller```를 구성했다.  
```Service```단에서 ```List```를 따로따로 만들어 가져와야 한다.  
따라서 ```WrappedDto```로 두 개의 ```List```를 감싸서 응답하도록 했다.  
```Service```단에는 아래와 같이 두 개의 함수로 분리해서 구현해 두었다.  

<img width="600" alt="스크린샷 2024-04-05 오전 1 12 23" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/3d515f86-7088-4819-ac55-e1f07b856ab4">  

### 결과
우선, 아래와 같이 아무 조회수가 없을 때에도 ```NPE``` 없이 잘 동작한다.  

<img width="474" alt="스크린샷 2024-04-05 오전 1 13 42" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/f1196d09-0f9a-4811-becd-a6755c2a2112">  

이제 로그인을 하고 조회수를 올려서 확인해보도록 하겠다.  
로그인을 한 뒤, 각 웹툰의 조회수가 잘 올라갔고 아래와 같이 랭킹도 잘 응답된다.  

<img width="436" alt="스크린샷 2024-04-05 오전 1 15 21" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/4431cb1a-0c7d-4317-a730-38bc702a3e98">  

이제 일단 대충 기본적인 기능은 구현되었다.  
이제 생각해야 할 것은 아래의 2가지가 있겠다.  

1. 한 유저당 하루에 한 번 씩만 조회를 하도록 제한할 것
2. 하루가 지나면 자동으로 ```ZSet```을 초기화 하도록 할 것

다음 글에서 한 번 고민을 해보도록 하자.  
