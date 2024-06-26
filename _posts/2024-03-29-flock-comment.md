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
아마 요즘 모바일 환경에서 유행하는 형태라고 생각된다.  

<img width="370" alt="스크린샷 2024-03-29 오후 2 36 10" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/a5aeb4a8-1ec5-451c-b89b-128c61a81242">  

그럼 우선 ```깊이``` 개념을 신경쓰지 않아도 되니, 그룹을 묶을 필요가 없다.  
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

```getComments()```의 경우엔, 댓글을 동시에 가져왔기 때문에 1개의 쿼리가 나간다.  
하지만 내가 적은 로직에서 ```getChilds()```의 경우를 생각해보자.  

현재 ```fetchType.LAZY``` 전략을 사용하기 때문에, 연관된 객체를 프록시로 가져온다.  
이후 실제 해당 객체가 사용될 때, 쿼리가 날아가서 실제 객체를 가져오는 것이다.  
이 개념이 ```getChilds()```에도 반복적으로 적용되었다고 보면 된다.  


#### Join이 필요한가?
```N + 1``` 문제가 발생했을 때 해결하는 방법은 여러가지가 있다.  
```Fetch Join```을 사용하거나, ```EntityGraph```, ```BatchSize``` 등...  
하지만 나는 ```Join```이 과연 필요한지 의문을 갖게 되었다.  

먼저 ```대댓글``` 기능에 과연 ```Join```이 왜 필요한지 생각해보자.  
```댓글``` 객체에 대해서, ```자식 댓글```을 합 번에 가져와 쿼리를 줄이기 위함이다.  
한 방 쿼리로 모두 불러오면, ```getChild()``` 메서드로 접근할 수 있게 되는 것이다.  

하지만 잠깐 생각해보면, ```댓글```을 그냥 불러와도 모두 불러와지지 않는가?  
```댓글 - 자식 댓글```간의 관계는 ```Self join```의 관계를 갖고 있다.  
그럼 ```댓글```을 모두 불러오면, 어차피 자식 객체도 불러와지게 된다.  
불러온 객체들을 ```서비스 로직```을 통해서 구분한 뒤, ```DTO```로 응답할 수 있지 않을까?  

<img width="1307" alt="스크린샷 2024-04-02 오전 2 23 29" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/d7950aa1-0f7d-4f97-9d85-d6ac97966241">  

위와 같이 두 개의 쿼리를 작성해 보았다.  
위의 쿼리는 ```Join``` 없이 그냥 댓글을 모두 불러오는 쿼리이다.  
아래의 쿼리는 ```Join```을 통해 댓글과 자식 댓글을 연결해 불러오는 쿼리이다.  

두 결과에 있어서 차이가 전혀 없음을 볼 수 있다.  
또한, ```Join```된 결과에 보이는 주 테이블만으로도 충분히 응답을 만들 수 있다.  
과연 ```Join```이 필요한 것인지는, 추후 좀 더 고민해보도록 하자.  

결국 아래와 같이 쿼리를 짜게 되었다.  

<img width="839" alt="스크린샷 2024-04-02 오전 2 25 33" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/9ab462a3-3bcb-48ab-9bf6-b9ef96a78977">  

또한, 아래와 같이 ```Serivce``` 단에서 로직을 구현하였다.  

<img width="786" alt="스크린샷 2024-04-02 오전 2 26 05" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/992c66db-e15f-44f1-afa4-f75fc42778a4">  

기본적으로 ```댓글```을 모두 조회한 뒤, 반복문을 통해 해당 댓글을 훑는다.  
훑으며 부모가 없는 댓글을 최상층 댓글로 인식하며, 응답 ```List```에 추가한다.  
단, ```HashMap```을 생성해 ```<ID, DTO>``` 형태로 저장하도록 하자.  

만약 부모가 있는 댓글이 나왔다면, ```HashMap```에서 자신의 부모를 조회한다.  
부모의 ```DTO``` 내에 존재하는 ```childList```에 본인의 ```DTO```를 추가하도록 하자.  
핵심은 ```HashMap``` 내의 ```DTO```와 응답 ```List```의 ```DTO```가 같은 객체라는 점이다.  
같은 객체이기에, 같은 주소를 공유하며 한 쪽을 변경하면 다른 쪽도 변하게 된다.  

결국 아래와 같이 우리가 원하는 형태로 ```DTO```를 만들 수 있는 것이다.  

<img width="433" alt="스크린샷 2024-04-02 오전 2 31 55" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/858c3a9a-34bc-4c76-b2f6-d59ced4f8be0">  

가장 중요한 ```N + 1``` 문제의 여부를 확인해보자.  
아래와 같이 단 ```2```개의 쿼리만이 발생했음을 확인할 수 있다.  

<img width="452" alt="스크린샷 2024-04-02 오전 2 33 37" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/9ae818f1-0ada-4dad-bc30-4d9ffc5da2b0">  

일단 원하는 대로 동작하도록 구현은 했지만, 좀 더 고민해볼 필요가 있다.  
왜 다들 ```Join```을 사용해서 구현하는 것인지,  
좀 더 깔끔하게 쿼리를 짜고 로직을 구현할 방법은 없을 지 고민해보도록 하자.  
