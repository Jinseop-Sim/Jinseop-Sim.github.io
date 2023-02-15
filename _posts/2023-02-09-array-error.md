---
layout: post
title: "DB의 배열 처리는 어떻게 해야 할까?"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
프로젝트를 진행하면서 좋아요 기능을 내가 맡게 되었다.  
내 생각에는 아래의 그림과 같이 Entity에 선언을 하면 될 것이라 생각했다.  
<img width="568" alt="멍청한 설계" src="https://user-images.githubusercontent.com/71700079/218949864-a9cf8f20-5821-40b8-b337-4172400c1192.png">  

```OnetoMany``` 단방향으로 설계한 이유는 아래와 같다.  
- User가 게시글에 좋아요를 누르고 자신의 배열에 기록한다.
- 하지만 게시글이 User를 참조해야하는 기능은 설계 대상에 없다.
  - 좋아요를 누른 User를 확인하는 기능(X) 
- 따라서 User만 좋아요를 누른 글의 기록을 들고 있으면 된다.

하지만 아래와 같은 문제가 발생했다.  
<img width="731" alt="좋아요 기능 오류" src="https://user-images.githubusercontent.com/71700079/218121347-0ece622d-88e9-49e7-9937-340506e5d8d6.png">  
<img width="497" alt="removed2" src="https://user-images.githubusercontent.com/71700079/218121365-c8e65b4c-5653-459b-8802-5c5965da4a25.png">  
<img width="738" alt="KakaoTalk_20230209_224323845" src="https://user-images.githubusercontent.com/71700079/218121360-b7d55343-c2ae-4f3a-a493-d345192fa022.png">  

사진에서 확인할 수 있듯이, 두 배열이 같은 배열 취급이 되고 있다.  
잠시 곰곰히 생각을 해보니 정말 기초적이고 바보같은 오류가 있었다.  
```@OneToMany```와 ```@JoinColumn``` Annotation으로 일단 관계를 지었으나,  
Join 대상 Column과 배열의 타입이 아예 일치함을 알 수 있다.  
내 생각엔, 그로 인해 내가 선언한 두 배열을 구분을 할 수 없는 것 같다.  

연관 관계는 Column 명을 보고 FK를 통해 맺어지게 된다.  
내가 사용한 FK는 작성한 글 목록은 ```(mappedby = user)``` 이므로 User ID.  
좋아요 누른 글 목록은 ```@JoinColumn(name = MEMBER_ID)``` 이므로 User ID.  
둘이 Join 대상 Column이 같고, ```List<PostType>``` 으로 Type도 일치한다.  

그럼 해당 게시글은 User ID를 외래키로 관계를 맺어버리니,  
해당 배열을 동시에 조작하게 되는 것이라고 생각한다.  
그럼 이 문제를 어떻게 해결할 수 있을까?  

### 결국 ManyToMany
내가 배운 바로는, ```OneToMany``` 단방향의 설계는 지양하는 게 좋다고 한다.  
그 이유는 FK를 ```Many``` 쪽에서 들고 있어야 하기 때문이다.  
그로 인해 객체를 저장할 때에만 해도, 불필요한 ```Update Query```가 날아가게 된다.  
외래키를 가진 ```Many``` 쪽에 FK가 없기 때문에 ```Update```를 하게 되는 것이다.  

하지만 양방향으로 설계를 한다고 해도, FK와 Type의 중복은 피할 수 없었는데,  
생각을 곰곰히 한 결과 그냥 ```ManyToMany``` 형태로 설계를 하기로 결심했다.  
내가 생각하기엔, 글에서 즐겨찾기 한 Member를 알 필요가 없는 구조이기 때문에,  
```ManyToMany```로는 설계하지 않으려 했는데 우선 설계를 해놓고 더 생각하기로 했다.  

아래와 같이 ```ManyToMany``` 로 교차 Entity를 만들어 설계했다.  
<img width="580" alt="변경" src="https://user-images.githubusercontent.com/71700079/218949898-0cfd1740-b782-47ce-a4ca-a02f390baaf1.png">  
<img width="583" alt="교차 엔티티" src="https://user-images.githubusercontent.com/71700079/218949884-e71df8d7-fbab-483a-a22b-104d73f8b08d.png">  

그 결과 아래와 같이 성공적으로 구현되었음을 확인할 수 있었다.  
<img width="499" alt="KakaoTalk_20230214_215918439" src="https://user-images.githubusercontent.com/71700079/218756697-9d183c01-13e2-45d4-bac4-d800495bb873.png">  
