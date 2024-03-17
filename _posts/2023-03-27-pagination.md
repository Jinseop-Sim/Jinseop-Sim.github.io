---
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

{% highlight sql %}

{% endhighlight %}
### JPA Pageable
