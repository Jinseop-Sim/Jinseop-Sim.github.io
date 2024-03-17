---
layout: post
title: "DB에 더미 데이터를 삽입해보자!"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
프로젝트를 진행하다 보면, DB에 더미 데이터가 필요할 떄가 있다.  
예를 들어, Pagination을 테스트하기 위해서 필요할 수가 있을 것이다.  
또 성능 체크 등, 다양한 이유로 필요할 때가 있다.  

어떻게 더미 데이터를 삽입할 수 있는지 공부할 필요가 있다!  

### DB 용량 확인
먼저, 더미 데이터를 집어넣으려면 용량을 확인해야 한다.  
가용 공간이 얼마나 남았는지 확인 후, 삽입을 해야한다.  
아래와 같이 가용 공간 확인이 가능하다.  
{% highlight sql %}
SELECT SUM(data_length+index_length)/1024/1024 "USED_MB", SUM(data_free)/1024/1024 "FREE_MB"
FROM information_schema.tables;
{% endhighlight%}

<img width="580" alt="스크린샷 2024-03-17 오후 4 38 34" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/01a4105c-e840-4321-88fd-9b7c23e08586">  

위의 쿼리는 DB의 전체의 용량과 사용 중인 용량을 반환한다.  
아래의 쿼리는 대상 Database별로 사용 중인 용량을 반환한다.  

{% highlight sql %}
SELECT table_schema "DB", ROUND(SUM(data_length+index_length)/1024/1024, 1) "USED_MB"
FROM information_schema.tables
GROUP BY 1;
{% endhighlight%}  

<img width="578" alt="스크린샷 2024-03-17 오후 4 38 53" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/b417cc40-99ae-473d-960d-284d7cde84da">  

충분한 용량이 있음을 확인했다면, 다음으로 넘어가자!  

### 프로시저
DB에서 사용할 수 있는 프로시저에 대해 알아보자.
