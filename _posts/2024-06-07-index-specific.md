---
layout: post
title: "Index of MySQL"
categories: Tech
tags: [theory]
author:
  - Jinseop Sim
toc: true
---
이번 포스팅에는 DB에서 가장 중요한 Index에 대한 이야기이다.  
언젠가 한 번 공부해야지 생각했었는데, 이번 기회에 공부해보려 한다.  

### Index?
기본적인 개념은 너무 유명하고, 추상적이다.  
간단히 말하자면 __조회__ 성능을 최적화 시키기 위한 도구!  
흔히 얘기하는 책의 목차를 만들어 관리하는 그런 개념이다.  

물론 개념적으로는 다들 알고 있지만, 실제 사용 방식을 알고 사용해야 한다.  
실제 사용 원리, 또 적용 방식에 대해 한 번 자세히 알아보자.  

### Index의 구조
실제 Index가 동작하는 원리를 알기 위해, 그 구조를 알아야 한다.  
나는 현재 MySQL을 기준으로 공부를 하고 있으니, InnoDB 엔진의 Index를 알아보자.  

#### B-Tree Index
InnoDB에서 기본 구조로 채택하고 사용하고 있는 B-Tree 구조의 Index이다.  
가장 큰 특징은 아래와 같이 여러개의 자식 노드를 가질 수 있음이 특징이다.  

<img width="782" alt="스크린샷 2024-06-07 오후 8 24 21" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/5b3f7104-4cd8-44cc-9926-fbeb6f7e00c1">  

또한, 리프노드의 데이터들이 링크드리스트와 같이 서로 연결되어, 범위 검색에 유리한 구조이다.  
사실 B-Tree라고 칭하지만 이 구조의 Tree는 엄밀히 말하자면 ```B+Tree```이다.  
인덱스는 키를 기준으로 항상 정렬된 상태로 유지가 된다.  
즉, 삽입과 수정이 빈번한 경우에는 재정렬 또한 빈번해지므로 인덱스를 걸지 않는 것이 좋다.  

### 실행 계획
MySQL에서는 ```EXPLAIN``` 키워드를 통해 실행 계획을 확인할 수 있다.  
인덱스와 관련해서는, 해당 쿼리가 인덱스를 타는지 안타는지 확인할 수 있게 되어있다.  
아래와 같이 ```Type``` 컬럼을 확인해서 인덱스를 거치는지 안 거치는지 확인할 수 있다.  

<img width="932" alt="스크린샷 2024-06-07 오후 9 42 24" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/ff1fc40f-869d-40ef-9c09-295182394a89">

위의 경우에는 ```Type const```의 경우로, 가장 빠르게 조회가 되는 경우이다.  
그 이유는 MySQL에서는 ```Primary key```를 기본적으로 ```Clustered Index```로 저장한다.  
이는 물리적인 레코드 위치까지 정렬하여 저장하기 때문에, 굉장히 빠르게 조회가 가능하다.  

아래의 경우는 PK가 아닌, 일반 컬럼을 통해 ```WHERE``` 절로 조회한 것이다.  
```Type ALL```로 지정되며, 이는 모든 테이블을 탐색해 찾아왔다는 의미이다.  

<img width="896" alt="스크린샷 2024-06-07 오후 9 45 29" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/45cabd66-a04b-4f9d-a248-1a1d54a67b74">  

<img width="565" alt="스크린샷 2024-06-07 오후 9 50 23" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/f37896d1-20bd-4013-a370-f78d60ee8f73">  

<img width="578" alt="스크린샷 2024-06-07 오후 9 50 39" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/3020ced7-204c-45e1-96c0-1ab6638c054a">  
