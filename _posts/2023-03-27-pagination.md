﻿---
layout: post
title: "Pagination에 대한 짧은 생각"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
현재 진행 중인 프로젝트에는 게시판에 Pagination 기능이 들어간다.  
나름 기본적으로 구현해야 할 기능 중 하나라고 생각한다.  

Pagination이 필요한 이유는 다음과 같다.  
- 만약 글이 100만건 정도 된다면?
  - 한 번에 모두 보내는 것은 성능 저하를 초래한다.
- Client 입장에서 생각해본다면?
  - 일정 수의 글 만큼 자르는 것이 보기 더 편할 것이다.

### Pagination 로직이 Frontend에 있다면?
Backend는 Query를 통해 전체 글을 보내기만 하면 되고,  
Frontend에 매번 전체 게시글을 불러온 뒤 구현해야 한다.  

이는 위에서 언급했던 Pagination이 필요한 이유에 어긋난다.  
글의 수가 많아질 수록 한 번에 보내는 데에 걸리는 시간이 길어지고,  
Frontend에서 처리해야 할 시간도 점점 길어지게 될 것이다.  

또한 Frontend에서 전체 데이터를 이미 호출 했으므로,  
어쩌면 데이터가 유출될 위험이 있을지도 모르겠다.  

결국은 품질 저하를 초래하는 방식임을 알 수 있다.  

### 그럼 Backend에 구현한다면?
Frontend는 버튼만 만들어 요청을 보내기만 하면 된다.  
단, 페이지를 넘길 때 마다 매번 요청이 들어와 Query가 날아간다.  

이 방식이 물론 위의 Frontend 로직 방식보다는 성능이 빠를 것이다.   
애초에 한 번에 보내야 할 글의 수가 현저히 줄어든다.  
그리고 특별한 로직 없이 Query문 만으로도 해결이 가능하다.  

하지만 내 고민은 이제부터가 시작이다.  
사용자가 많아지면, Query가 날아가는 횟수가 굉장히 늘 것이다.  
그럼 과연 Query 자체 성능에 있어서는 문제가 없을까?  
수 많은 사용자가 조회를 할 때에는 Query 자체의 성능이 중요할 것이다.  

그래서 아래로 Pagination의 종류에 대해서 포스팅하려고 한다.  

### Offset based pagination
먼저, ```Offset based pagination```이다.  
꽤 많이 사용되는 방식이며, 이름 그대로 ```LIMIT, OFFSET```을 사용한다.  
아래와 같은 Query를 통해 구현할 수 있다.  

```SELECT * FROM BOARD LIMIT 40 OFFSET 80```  
```SELECT * FROM BOARD LIMIT 80, 40```   

위의 두 Query는 동일하게 80번 이후로 40개의 데이터를 불러온다.  
실제로 SQL이 아닌 코드 내에서 적용할 땐, 아래와 같이 구현할 수 있다.  

<img width="678" alt="스크린샷 2024-03-17 오후 4 06 37" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/5af542fc-a8bc-420c-acd0-9dcc257bfe2d">  

위의 사진은, 실제로 프로젝트에 사용된 JPQL로 구현된 Pagination 코드이다.  
```setFirstResult(), setMaxResult()```를 통해 ```LIMIT, OFFSET```을 설정할 수 있다.  
__Postman__ 을 통해 요청을 날려보면 아래와 같은 Query가 날아간다.  

<img width="432" alt="스크린샷 2024-03-17 오후 4 08 56" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/121ceb8e-fbbc-4c63-99c2-420654d9eef3">  

Query의 끝에 ```LIMIT``` 구문이 추가되어 있음을 확인할 수 있다.  
하지만 이 Query에는 성능적인 면에서 치명적인 단점이 있다.  

```OFFSET```의 원리는 데이터를 불러온 뒤 삭제하는 것이다.  
위의 예시를 들면, ```120```개의 데이터를 불러온 뒤 ```80```개를 삭제하는 것이다.  
데이터가 많아져 ```LIMIT 40 OFFSET 1,000,000```를 요청한다고 생각해보자.  
그럼 우리는 ```1,000,040```개의 데이터를 불러온 뒤 삭제해야 하는 것이다.  

결국 데이터가 많을 수록 시간은 느려지고, 부하를 받게 된다!  

### Cursor based pagination
위의 이유로 등장하게 된 것이 ```cursor based pagination``` 방식이다.  
```OFFSET```을 사용하지 않고, ```WHERE``` 절로 조건을 걸어 요청한다.  
아래와 같이 Query를 요청하면, 아래의 표와 같이 결과가 나올 것이다.  
{% highlight sql %}
SELECT *
FROM BOARD
WHERE ID < 1001
ORDER BY ID DESC LIMIT 5;
{% endhighlight %}  

<img width="249" alt="스크린샷 2024-03-17 오후 4 13 36" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/b7ac7e2d-861d-4925-8735-9149e692659c">  

그럼 위의 Query에 이어서 아래와 같이 요청한다면?  
{% highlight sql %}
SELECT *
FROM BOARD
WHERE ID < 996
ORDER BY ID DESC LIMIT 5;
{% endhighlight %}  

<img width="247" alt="스크린샷 2024-03-17 오후 4 15 03" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/570b6211-011d-463f-98d5-d6a13beca00e">  

위와 같은 표처럼 Query의 결과가 출력될 것이다.  
Pagination을 한 효과와 동일하게, 최신순으로 5개씩 잘려 결과가 반환되었다.  
이제 해당 Query를 일반화하면 아래와 같이 요청할 수 있다.  
{% highlight sql %}
SELECT *
FROM BOARD
WHERE ID < {마지막으로 요청한 ID}
ORDER BY ID DESC LIMIT 5;
{% endhighlight %}  

마지막으로 요청한 ```ID```를 계속 갱신하며 요청하는 것이다.  
해당 방식은 ```OFFSET```과 다르게 데이터를 모두 불러오지 않는다.  
또한 ```WHERE``` 절로 조건을 걸어, ```Index```도 태울 수 있다.  
따라서 ```Offset based```에 비해 훨씬 효율적이다!  

#### 실제 성능 테스트
DB에 200개의 더미 데이터, 100만개의 더미 데이터를 넣고 요청을 보내 보았다.  
아래의 결과들은 200개의 더미 데이터에 대한 비교이다.  

- ```Offset based pagination```

<img width="597" alt="스크린샷 2024-03-17 오후 5 21 42" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/22c8179d-f6a4-41e8-8cad-202e85e2b391">  

- ```Cursor based pagination```  

<img width="561" alt="스크린샷 2024-03-17 오후 5 21 20" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/b9f2dde2-c8f7-4844-bfec-d8e156a46738">  

적은 데이터에서는 ```offset based pagination```이 더 빠름을 확인할 수 있다.  

아래의 결과들은 100만개의 더미 데이터에 대한 비교이다.  
아래의 사진에서 100만개의 데이터가 생성되었음을 확인할 수 있다.  

<img width="437" alt="스크린샷 2024-03-17 오후 5 30 30" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/8c8282a9-b86c-4006-908a-623aba130ccc">  

- ```Offset based pagination```

<img width="685" alt="스크린샷 2024-03-17 오후 5 32 23" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/beb62c79-637a-4381-bea5-cd746940adcc">

- ```Cursor based pagination```  

<img width="602" alt="스크린샷 2024-03-17 오후 5 32 40" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/3e17479c-3d59-47ba-abab-7f58f139f30e">  

위의 두 Query 모두 ```1 ~ 40```번 데이터를 요청하였다.  
그 말은, 최신 순으로 가장 오래된 데이터 ```40```개를 훑은 것이다.  
이번에는 확실히 ```cursor based pagination```이 빠름을 확인할 수 있다.  

즉, 데이터가 많아질 수록 ```cursor based pagination```이 유리하다!  
그럼 이 방식을 코드에서 어떻게 구현할 수 있을까?  

### JPA Pageable
```Spring JPA```에서는 ```Pageable```이라는 굉장히 유용한 객체를 제공한다.  

```Pageable``` 객체를 사용하지 않는 경우엔, 아래와 같이 구현될 것이다.  
프론트엔드 단에서 가장 마지막에 받은 커서를 요청에 함께 보내야한다.  

<img width="911" alt="스크린샷 2024-03-18 오전 2 41 40" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/04913035-cf56-495c-9fc6-ffc6e4d7cf7c">  

그 결과 아래와 같이 10개씩 최신순으로 불러옴을 볼 수 있다.  

<img width="578" alt="스크린샷 2024-03-18 오전 2 42 45" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/3cad1234-594a-4a07-8542-4b80ba3f4e30">  

```JPARepository```를 사용하는 경우엔, 훨씬 깔끔하게 코드 작성이 가능하다.  
```Pageable``` 객체는 ```JPARepository```에서 Pagination을 구현하도록 한다.  
먼저, 아래와 같이 ```Repository```를 ```Interface```로 구현한다.  

<img width="651" alt="스크린샷 2024-03-18 오후 7 37 06" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/9f9ea45e-c613-4d5c-97b4-592e65fb5007">  

```JPARepository```는 메서드의 이름을 ```SQL```로 매핑하는 ```ORM```이다.  
위의 경우, 매우 간단하게 한 줄로 ```id``` 기준 오름차순으로 게시글을 가져올 수 있다.  

아래와 같이 __Postman__ 을 통해 ```/test?page=1&size=10```의 경로로 요청을 보내면?  
그 아래의 사진과 같이 2개의 Query가 날아감을 확인할 수 있다.  
또한 요청의 결과가 원하는 대로 ```999990 ~ 999981```번 게시글임을 확인할 수 있다.  

<img width="287" alt="스크린샷 2024-03-18 오후 7 41 41" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/d0b4ac0c-1989-4b61-8e4c-50f4283926d5">  

<img width="626" alt="스크린샷 2024-03-18 오후 7 41 30" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/137c830d-b50c-42fe-af1a-48f4ecb8b304">  

<img width="337" alt="스크린샷 2024-03-18 오후 7 42 25" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/d245defd-da2e-4aa3-9e26-60dd39d6c821">  

이렇게 ```JPARepository```는 매우 간편한 Query를 지원한다.  
하지만 복잡한 요청을 보내야 하는 경우, ```JPARepository```만으로는 처리할 수 없다.  
```JPQL, QueryDSL```과 같은 기능을 통해, 직접 Query를 작성해야 할 때도 있다.  
