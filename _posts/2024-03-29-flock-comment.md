---
layout: post
title: "댓글의 대댓글 기능 구현하기"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
기존의 ```Flock project```에서는 댓글 기능 밖에 없었다.  
그래서 대댓글 기능을 한 번 추가해보려고 한다.  

### Entity
먼저, ```Entity```를 어떤식으로 구성해야 할까?  
자식 댓글을 보관하기 위한 새 테이블을 만들어야 할까?  
그럴 필요는 없다고 생각된다.  

우선 나는 인스타그램과 같이 깊이가 하나인 대댓글을 구현할 것이다.  
바로 아래의 형태와 같이 말이다.  

<img width="370" alt="스크린샷 2024-03-29 오후 2 36 10" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/a5aeb4a8-1ec5-451c-b89b-128c61a81242">  

그럼 우선 ```깊이``` 개념을 신경쓰지 않아도 된다.  
단순히 ```DB```에서 현재 댓글을 ```부모```로 가진 댓글을 모두 가져오면 된다.  
그러기 위해서, 우선 필드에 ```부모 댓글``` 필드를 만들어 주었다.  

<img width="758" alt="스크린샷 2024-03-29 오후 4 15 07" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/ff18346b-10f9-484a-81ba-5282b8fed646">  

이후 댓글을 모두 조회해 올 때를 생각해서 위와 같이 선언했다.  
게시글을 조회할 때, ```댓글, 대댓글```이 모두 한 번에 조회되어야 한다.  
```댓글```을 조회할 때, 해당 ```id```를 부모로 하는 댓글을 함께 조회하도록 하자.  

#### 양방향 관계?
그 과정속에서 고민이 하나 생겼다.  
과연 ```다대일, 일대다```의 경우 양방향 관계를 유지하는 것이 좋은가?  

<img width="676" alt="스크린샷 2024-03-29 오후 2 53 25" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/92d5199c-cd4b-4476-ae34-45ec121f2ead">  
<img width="434" alt="스크린샷 2024-03-29 오후 2 53 39" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/8567a407-405f-48e6-9a06-e9c8a9e61074">  

위와 같이 ```Member, Webtoon``` 엔티티에는 댓글 목록이 들어있다.  
이후 댓글을 조회할 때 DB를 거치지 않기 위해, 저장시켜 놓으려고 했다.  
이런 양방향 관계는 좋지 않은 것일까?  

양방향으로 유지하는 경우, 양쪽 ```Entity```에 객체를 꼭 추가 해주어야 한다.  
이 부분을 누락할 위험성 때문에 안좋다고 볼 수도 있겠다.  
하지만 추후 게시글 삭제, 회원 탈퇴 시에 연관된 객체를 모두 한 번에 지워야 한다.  
그런 경우에는 양방향으로 걸고 ```CASCADE``` 옵션을 사용하면 매우 편리하다.  

나는 양방향으로 선언하고 사용해도 무방하다고 생각한다.  

### N + 1 및 문제 발생
```Service, Controller```를 모두 구현하고 보니,  
내가 얼마나 생각없이 코드를 짰는지 알 수 있었다.  
곧바로 아래와 같이 ```N+1 Query``` 문제가 발생했다.  

<img width="510" alt="N+1 Prob" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/274afbf8-6ff6-4351-8421-e76c6a5fe471">  
또한 게시글을 조회하는 로직을 이상하게 짜, 댓글도 이상하게 조회되었다.  

<img width="520" alt="Comment probl" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/73205c22-b70a-43a3-9d68-798431842bbe">  

먼저 기존에 짜놓은 로직을 보도록 하자.  
웹툰의 상세 페이지를 불러올 떄, 아래와 같이 댓글을 조회하도록 했다.  

<img width="810" alt="스크린샷 2024-03-29 오후 4 18 27" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/fa6db692-4b03-4667-854a-78eaf3d82020">  

댓글을 먼저 불러온 뒤, 댓글의 DTO에서 대댓글을 조회한다.  
지금 다시 생각해보면 정말 바보같은 로직이다.  

<img width="638" alt="스크린샷 2024-03-29 오후 4 19 12" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/78628ca6-f4b8-4e2b-b906-69793f249328">  

위와 같이 댓글을 조회해 버리면, 뭐가 문제일까?  
생각해보면 단순히 배열에 있는 ```댓글```을 조회하는 것이 아니다.  
```연관 관계```가 맺어져 있는 테이블에서 ```댓글``` 컬럼을 조회해오는 것이다.  
그럼? ```대댓글``` 또한 ```댓글``` 테이블에서 조회를 해와야 하는 것이다.  

#### JPA의 DB 조회
JPA가 DB에서 값을 조회해오는 원리를 한번 상기해보자.  
```영속성 컨텍스트``` 개념과 ```1차 캐시``` 개념을 알아야 할 것이다.  

JPA에는 아래의 그림과 같이 기본적으로 ```영속성 컨텍스트```가 존재한다.  

![image](https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/198b7ec2-68e4-437a-913d-b38cfe49b2b7)  
> 출처 : [Baeldung JPA/Hibernate Persistence Context](https://www.baeldung.com/jpa-hibernate-persistence-context)

영속성 컨텍스트는 아래와 같은 역할을 수행할 수 있다고 한다.  
![image](https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/e3f5f699-05de-4620-b044-873f577271a8)  

1. 캐시
2. 변경 감지
3. 지연 로딩

위의 기능들과 함께 ```Entity```들의 생명주기를 관리할 수 있는 장치가 된다.  
우리가 주목해야 할 기능은 ```캐시```로서의 기능이다.  
어떤 정보를 ```find()``` 했을 때, JPA는 ```영속성 컨텍스트```를 먼저 조회한다.  
만약 ```캐시```에 없는 정보라면, ```DB```에 접근하기 위해 쿼리를 날린다.  

그럼, 다시 ```댓글 대댓글``` 기능으로 돌아가보자.  
나는 지금 ```Webtoon```을 찾아와 ```getComment()```를 했다.  
또한 ```getComment()```에 대해 ```DTO``` 내부에서 ```getChilds()```를 했다.  
```Comment```나 자식 ```Comment```에 대한 정보는 이전에 조회를 한 적이 없다.  

다시 말하면, ```캐시```에 존재하지 않는 정보라는 말이다!  
그럼 당연히 ```DB```로 쿼리를 날려서 해당 정보들을 가져와야 할 것이다.  

그럼 조회를 할 때 ```캐시```에 존재하는지 어떻게 판단할까?  
바로 ```Primary Key```를 통해서 동일한 값인지 판단하게 된다.  
지금 내가 조회하려는 객체의 ```PK```가 이미 캐시에 있으면, 해당 객체를 반환한다.  

```getComments()```의 경우엔, 한 번에 연관된 댓글을 모두 가져오기 때문에
하지만 내가 적은 로직에서 ```getChilds()```의 경우를 생각해보자.  
