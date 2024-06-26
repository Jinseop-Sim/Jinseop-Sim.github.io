---
layout: post
title: "랭킹 서비스 개발 2 (w. Redis)"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
저번에 이어 랭킹 서비스를 마저 개발해보려고 한다.  
내가 이제 해결해야 할 과제는 아래의 두 개와 같다.  

1. 회원 한 명당 각 게시글에 대해 조회수는 한 번만 올릴 수 있다.
2. 하루가 지나면 자동으로 ```Sorted Set```을 비울 수 있어야 한다.

1번부터 먼저 구현해보도록 하자.  

### 조회수 중복 방지
조회수 중복을 방지하기 위해서는 어떤 방법을 적용할 수 있을까?  
가장 간단하게 생각할 수 있는 것은 ```DB```에 체크하는 것일것이다.  
각 게시글들이 ```DB```에 조회한 ```User```를 가지고 있는 것이다.  
그럼 지금 로그인한 유저가 연관되어 있는지 조회 후, 방지를 할 수 있을 것이다.  

하지만 이 방식은 성능 저하를 초래할 확률이 너무 크다.  
조회를 할 때 마다 ```Update``` 쿼리가 날아갈 것이기 때문에, 성능이 저하된다.  

#### 쿠키
검색을 조금 해보니, 쿠키나 세션을 이용하는 방법들을 볼 수 있었다.  
쿠키를 이용하는 경우엔, 쿠키에 ```[1]_[2]```와 같이 문자열을 저장한다고 한다.  
해당 문자열을 읽어와서 ```Tokenize``` 시켜 조회했던 글을 확인한다.  
대부분은 이 방식을 채택해서 사용하는 것 같아 보였다.  

하지만 이 방식에는 큰 단점이 있다.  
바로 브라우저에 종속된다는 문제와 용량 문제이다.  
```Chrome``` 브라우저의 쿠키는 기본적으로 하나당 ```4MB``` 정도 된다고 한다.  
이는 턱없이 적은 용량이라고 볼 수 있다.  

만약 사용자가 늘어나고, 게시글이 늘어나게 되는 경우 유지가 불가능할 것이다.  
또한 말했다시피 브라우저에 종속적이며, 탈취의 가능성이 매우 높은 방식이다.  
다른 방식을 생각해봐야 할 것 같다.  

#### Redis  
조회수를 올리는데에 사용했던 ```Redis```를 그대로 쓸 수는 없을까?  
이전에 말했듯이, ```Redis```는 매우 다양한 자료구조를 지원한다.  
이 경우엔 정렬과 같은 기능은 필요 없으니, ```Hash, List```를 사용할 수 있겠다.  
내 생각에는 ```List```에 ```Key : Value``` 형식으로 저장하는게 좋을 것 같다.  

이제 ```Redis```의 최대 용량에 대해서도 생각해보도록 하자.  
공식 홈페이지에서는 ```32bit```의 경우와 ```64bit```의 경우에 지원 용량이 다르다고 한다.  
```32bit```는 최대 ```3GB```이고, ```64bit```의 경우엔 제한이 없다고 한다.  

<img width="713" alt="스크린샷 2024-04-07 오전 11 29 25" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/6285c169-920d-4b54-85de-371710d9fcc3">  

제한이 없다는 것은, 무제한으로 메모리를 쓸 수 있다는 말일까?  
아니다. ```Redis```에서 자동으로 디스크에 접근을 해서 ```Swap```까지 이용한다는 말이다.  
그래서 ```Configuration```에는 ```maxmemory-policy```를 직접 설정할 수 있다.  
```Noeviction, LRU, LFU``` 등의 페이지 교체 알고리즘을 직접 정하는 것이다.  

그래서 ```maxmemory```와 ```maxmemory-policy```를 잘 설정해야 할 것 같다.  
지금은 소규모라 괜찮겠지만, 규모가 커졌을 때 매우 중요하게 작용할 것 같다.  

또한 공식홈페이지에서는 아래와 같이 기술하고 있다.  
```Associative array```는 최대 ```2^32```개의 키와 ```2^32```개의 값을 가질 수 있다.  

<img width="710" alt="스크린샷 2024-04-07 오전 11 21 06" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/73d92843-b22b-4eb8-9abc-a55c9cd8293d">  

```Redis```를 이용해서 개발하기에 용량은 매우 충분할 것 같다!  
이제 이를 통해서 기능을 개발해보도록 하자.  

### 서비스 적용
```Spring Redis```에서 제공되는 ```opsForList``` 기능을 사용할 것이다.  
```List```의 ```Key```를 사용자 아이디로, ```Value```를 게시글 번호로 했다.  
```Key```를 사용자 아이디로 한 이유는, 게시글이 훨씬 더 양이 많을 것이기 때문이다.  

아래의 ```checkViews``` 함수를 통해 ```List```에 게시글 번호를 넣었다.  
```Key```를 ```VIEWS:[USERID]```와 같은 형태로 만들어 판별하게 했다.  
매개변수로 ```userId```를 받아, 해당 사용자의 기록에 남기도록 한 것이다.  

<img width="587" alt="스크린샷 2024-04-07 오전 3 16 03" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/a2a19277-d3c1-4ccb-bfd7-5277b84254a4">  

이제 아래의 ```validate``` 함수를 통해 조회를 했는지 안했는지 검증한다.  
기본적으로 ```opsForList()```에서 제공하는 ```indexOf()``` 메서드를 사용했다.  
현재 사용자가 조회한 웹툰의 아이디를 ```List``` 내에서 찾을 수 있는 것이다.  
만약 없다면 함수의 동작에 의해 ```-1```이 반환되므로, 해당 결과를 ```boolean```으로 반환한다.  

```null```인 경우에는 ```List``` 자체가 없음을 의미한다.  
이는 첫 방문을 의미하니, ```True```를 반환해서 조회수가 증가하도록 해야 한다.  

<img width="736" alt="스크린샷 2024-04-07 오전 3 15 45" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/2aa096ca-2d21-486c-8301-48fbe2457d63">  

```if (findedIndex == null)```이 ```Always False```라는 경고가 뜨고 있다.  
```Long``` Type으로 선언했기 때문에 ```null``` 값이 분명 들어갈 수 있을텐데 왜 뜨는걸까?  
확인을 하기 위해 ```Redis```를 직접 비우고, 다시 조회해 보았다.  

<img width="970" alt="스크린샷 2024-04-07 오후 12 17 04" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/f8191b13-344a-4e08-b858-eae0a6ed02e7">  

로그를 찍어 상태를 보려하니, ```NPE```가 발생했다.  
그 말인 즉, ```findedIndex```가 ```null```로 들어왔다는 말이 된다.  
왜 저런 경고가 뜨는 것인지는 알 수 없지만, 우선은 그냥 진행해보도록 하자.  

### Spring Scheduler
이제 하루마다 랭킹을 초기화 하고, 조회한 게시글 목록을 초기화 해야 한다.  
이런 동작을 위해 ```Spring``` 에서는 ```Scheduled```라는 아주 멋진 기능을 제공한다.  
아래와 같이 ```@Scheduled``` 어노테이션을 통해 쉽게 사용할 수 있다.  

<img width="292" alt="스크린샷 2024-04-07 오후 12 20 32" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/4723bdc9-8a0c-481c-8d40-c835367c7526">  

또한 옆에 적힌 ```cron```과 같이, 4가지의 옵션을 추가로 사용할 수 있다.  

1. ```fixedRate``` : 일정 주기마다 해당 작업을 반드시 실행하는 옵션
2. ```fixedDelay``` : 작업을 마친 후 부터 해당 시간이 지나면, 다시 작업 실행
3. ```initialDelay``` : 초기에 해당 시간이 지나야 메서드가 실행되도록 설정하는 옵션
4. ```cron``` : Cron 표현식을 통해 특정 시간, 날짜를 구체적으로 설정할 수 있다.

#### Cron 표현식
아래와 같은 의미의 필드들이 있다.  

<img width="855" alt="스크린샷 2024-04-07 오후 1 54 35" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/7950949a-e5ed-4d65-bb37-41517e866fcf">  

> 출처 : [SpringDocs - CronExpression](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/support/CronExpression.html)

또한, 각 필드는 아래와 같은 값들을 가질 수 있다.  
- ```*``` : 모든 값을 의미한다.
- ```?``` : 특정한 값이 없음, 아무 값이나 다 가능하다.
- ```-``` : 범위를 나타낼 때 사용한다.
  - ```0 0-5 14```와 같이 사용하면, 2시에서 2시 5분까지 매분마다 작업을 수행한다.
- ```,``` : 특정 값들을 나열할 때 사용한다.
  - ```0 0 0 * * MON, WED, FRI```와 같이 사용하면, 월, 수, 금 자정에 작업을 수행한다.
- ```/``` : 시작 시간의 단위를 결정한다.
  - ```0 0/5 * * * ?```와 같이 사용하면, 매 5분마다 작업을 수행한다.
- ```L``` : 일에서 사용하면 마지막 일, 요일에서 사용하면 마지막 요일이 된다.
  - ```0 0 15 L * ?```와 같이 사용하면, 매월 말일마다 작업을 수행한다.
- ```W``` : 가장 가까운 평일을 의미한다.
  - ```0 0 15 15 * ?```와 같이 사용하면, 15일에서 가장 가까운 평일 3시에 작업을 수행한다.
- ```#``` : 몇째주의 무슨 요일인지를 의미한다.
  - ```0 30 10 ? * 3#3```와 같이 사용하면, 세번째 주 수요일 10시 반에 작업을 수행한다.

우선 나는 매일 ```자정```에 동작을 수행해야 하므로, ```Cron```을 사용할 것이다.  
```0 0 0 * * ?```와 같이 선언하면 ```매일 0시 0분 0초```에 작업을 수행하게 된다.  
이제 ```Redis```에서 기능을 찾아, 적절히 지워주도록 하자!  

#### 랭킹 초기화
우선 랭킹을 초기화 하는 함수는 아래와 같이 구현하였다.  
단순히 ```Key```를 삭제하는 동작이기 떄문에, 매우 단순하게 구현할 수 있었다.  

<img width="297" alt="스크린샷 2024-04-07 오후 2 04 52" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/43fc90ba-1db1-42e2-ac2c-d861e1c19c79">  

실제로 기능이 잘 동작하는지 테스트하기 위해 ```cron```을 수정했다.  
```cron = 0 0/1 * * * ?```과 같이 수정해, 1분마다 기능이 동작하도록 해보았다.  

<img width="599" alt="스크린샷 2024-04-07 오후 2 40 22" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/c4d40ca6-75e7-4deb-8dff-b94f11f59d20">  

현재 시각 ```오후 2시 40분``` 기준으로 ```POST_RANKING```이 있음을 볼 수 있다.  
아래는 ```1분```이 지난 ```오후 2시 41분```의 모습이다.  

<img width="593" alt="스크린샷 2024-04-07 오후 2 41 29" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/43c009e3-9f72-4f10-9b38-5aed6ce8e333">  

성공적으로 ```POST_RANKING``` 배열이 사라졌다!  
이로써 ```Scheduled```가 잘 동작하고 있음이 확인되었다.  

#### 조회 기록 초기화
조회 기록을 초기화하는 부분이 문제이다.  
모든 유저의 조회 기록을 초기화 할 수 있어야 한다.  
그러기 위해서, ```Redis```에서 제공하는 ```keys()``` 메서드를 사용하려 한다.  

<img width="525" alt="스크린샷 2024-04-07 오후 2 13 58" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/a9423f0a-9dd6-4367-b17b-f117364bb03f">  

현재 ```Redis```에는 위와 같이 ```VIEWS:1```이라는 키가 하나 존재한다.  
테스트를 위해서, 임의의 계정을 더 생성해 3개의 조회 기록을 남겨보자.  
아래와 같이 추가로 계정을 생성하고 조회하여, 조회 기록 ```List```가 3개가 되었다.  

<img width="523" alt="스크린샷 2024-04-07 오후 2 16 27" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/2dc823ae-b01f-4af8-afe1-c45db1bed410">  

이제 해당 조회 기록을 모두 불러올 수 있는지 테스트를 해보자.  
아래와 같이 ```Assertion```을 통해 조회 기록의 크기를 검증하도록 했다.  
무사히 크기 ```3```을 반환하는 것이 확인되었다!  

<img width="707" alt="스크린샷 2024-04-07 오후 2 17 12" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/af242217-6ca3-4265-95db-e6d6c68b3ca3">  

반환된 ```Set<String>```의 내용물을 확인해보면 아래와 같다.  
정상적으로 조회 기록과 관련된 모든 ```Key```를 불러오는 것을 볼 수 있다.  

<img width="308" alt="스크린샷 2024-04-07 오후 2 18 32" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/c9728ffa-a348-4770-9343-240307f19a10">  

이제 우리는 반환된 ```Key```에 해당하는 값들을 모두 지워주기만 하면 된다!  
바로 아래와 같이 ```delete(Collections<Keys>)```의 형태로 해당하는 자료구조들을 삭제할 수 있다.  

<img width="516" alt="스크린샷 2024-04-07 오후 2 19 26" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/87775e5d-375b-460a-b973-6f9aa3b32e1b">

### Keys? Scan?
조금 조사해보니, ```redisTemplate.keys()``` 메서드는 ```O(N)```의 시간복잡도를 갖는다.  
그래서 전체 데이터를 싹 다 불러오는 것이기 때문에, 데이터가 많을 수록 선형적으로 느려진다고 한다.  

하지만 ```redisTemplate.scan()```을 사용하는 경우엔, 데이터 수에 제한을 둘 수 있다고 한다.  
실제 ```RedisTemplate```의 코드를 열어보면, 아래와 같은 클래스가 포함되어 있다.  

<img width="372" alt="스크린샷 2024-04-07 오후 2 22 32" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/c5da3ef3-cfd5-48a2-a8bd-fe921358c53a">  

데이터의 수, 패턴, 타입 등을 지정해서 조회할 수 있는 것이다.  
```count```를 지정하게 되면, 해당 카운트만큼의 자료씩 차근차근 조회한다고 한다.  
만약 1000개의 자료가 있다고 가정하면, 10개씩 자료를 훑을 수 있는 것이다.  

원래라면 ```Full scan```을 하기 때문에, 중간에 다른 작업을 할 수가 없게 된다.  
기본적으로 ```Redis```도 ```DB```이기 때문에 ```Lock```을 걸고 동작한다.  
그래서 중간에 다른 작업을 할 수가 없는 것이다.  
하지만 ```scan()```을 사용하게 되면, 중간중간에 다른 작업을 할 수 있는 이점이 있다.  
