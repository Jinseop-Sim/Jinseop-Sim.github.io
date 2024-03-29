﻿---
layout: post
title: "다대다 관계와 조회 Query에 대한 고찰"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
프로젝트를 진행하던 중, 큰 고민을 하게 됐다.  
공동 구매기능을 구현하던 중, 회원이 참여 중인 글을 과연 어떻게 불러올 지가 고민이었다.  

우리 프로젝트는 회원과 참여 중인 공동구매가 ```M:N(다대다)``` 관계를 갖는다.  
회원은 여러 글에 참여할 수 있으며, 공동 구매는 여러 회원이 참여하기 때문이다.  
그래서 둘 사이에 ```Intersaction``` Table을 만들어 풀어 놓았는데,  
회원의 개인 마이페이지를 만드는 과정에서 참여 중인 글을 불러오는 기능이 있는데, 여기서 고민이 생겼다.  

```Intersaction``` Table에는 회원의 ID와 공동 구매 글의 ID만이 저장되어 있다.  
그렇다면 매번 글을 불러올 때마다 ID에 맞는 글을 Query를 날려 불러와야 하는가?  

### N+1 Query
현재 나는, 아래의 사진과 같이 ```Member``` Entity에 배열을 선언 해놓았다.  

<img width="713" alt="KakaoTalk_20230125_184932827" src="https://user-images.githubusercontent.com/71700079/214566423-24b69ce8-33c1-4ce7-a150-61bcaedaf13b.png">  
<img width="705" alt="KakaoTalk_20230125_184952339" src="https://user-images.githubusercontent.com/71700079/214566429-52670a3e-7f01-47b4-9acd-dd5bd89f01a3.png">  

내가 어떤 글에 참여를 하게 되면, 해당 배열에 해당 글의 객체가 추가가 되는데,  
중요한 것은 이 때, 글의 객체가 아닌 ```Intersaction```을 저장하고 있다는 것이다.  

그렇다면 만약 내가 참여중인 글 목록을 Client Side로 보내주고 싶다면?  
```Intersaction``` 마다 __글 ID__ 가 저장이 되어 있으니,  
```Intersaction.getId().getTitle()``` 과 같이 Method Chain을 이용하면 될까?  
만약 이렇게 Query를 날린다면, Id마다 Query가 날아가는 __N+1 Query__ 현상이 발생할 것이다.  

어떤 방식을 이용해야 조회 성능을 떨어트리지 않고 글을 가져올 수 있을까?  

### Fetch Join
첫번째로 떠오른 것은, __Fetch Join__ 이었다.  
__N+1 Query__ 를 해결하는 가장 간단한 방법으로 배웠던 Join 방식이다.  
연관 관계가 있는 Entity들을 하나의 Query로 Join 시켜 모두 영속시켜 놓는 방식인데,  
이 방식으로 Repository에 Method를 따로 만들어 글을 가져온다면?  
__N+1 Query__ 를 해결할 수 있을 것이라고 예상하며, 실행에 옮겼다.  

아래와 같이 __Fetch Join__ 을 통한 __JPQL__ 을 작성했다.  
<img width="905" alt="KakaoTalk_20230125_214912374" src="https://user-images.githubusercontent.com/71700079/214570406-83861706-109c-4c05-a118-c5e2db23f420.png">  

해당 Query의 결과를 이용할 수 있도록 ```Service``` 단에 Method를 만들었다.  
<img width="704" alt="KakaoTalk_20230125_214854719" src="https://user-images.githubusercontent.com/71700079/214570377-3ac1d59a-5ad4-4f0a-a199-23bd6ab1c0b6.png">  

실행을 시켜보니, 다음과 같이 __INNER JOIN__ 이 됨을 확인할 수 있다.  
<img width="651" alt="KakaoTalk_20230125_200813543" src="https://user-images.githubusercontent.com/71700079/214570429-efa14615-3378-4d81-bc7e-85491b6e5bbd.png">  

아무리 현재 내가 참여 중인 글의 수를 늘려도,  
해당 API에 요청이 들어올 때 나가는 Query가 총 4개였는데, 다음과 같았다.  

1. Member의 정보를 따오는 Query
2. Intersection Fetch Join Query
3. ```GroupBuyingIntersection```을 DTO로 변환할 때 Member를 불러오는 Query
4. DTO로 변환할 때 ```GroupBuying```의 정보를 불러오는 Query

곰곰히 생각을 해보니, 3번의 Member를 불러오는 과정에서도 문제가 발생할 것 같다.  
이 부분도 Fetch Join으로 해결하면 될 것 같은데,  
이 부분에 대해서는 조금 더 생각해 본 뒤 적용을 시켜보자.  