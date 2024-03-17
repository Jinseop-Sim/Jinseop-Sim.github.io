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
우리가 프로그래밍을 할 때 __프로시저__ 라는 단어를 사용한 적이 있을 것이다.  
아마 __함수(Function)__ 와 유사한 단어로 사용하였을 것이다.  
DB에서의 프로시저도 동일한 의미를 가진다.  

우리가 계획한 절차대로 수행할 명령들을 모아놓은 것이다.  
아래의 구문들을 사용해서, 프로시저를 구성할 수 있다.  

- ```SHOW PROCEDURE STATUS``` : 프로시저 목록을 확인할 수 있다.
- ```CREATE PROCEDURE [이름](파라미터1, 파라미터2, ...)``` : 프로시저를 생성한다.
- ```DROP PROCEDURE [이름]``` : 해당 프로시저를 삭제한다.
- ```CALL [이름](파라미터1, 파라미터2, ..)``` : 생성한 프로시저를 호출한다.
- ```BEGIN END``` : 프로시저의 시작과 끝을 정의하는 구문
- ```DECLARE [변수] INT DEFAULT [값]``` : 프로시저 내의 변수를 선언한다.
- ``` SET [변수] = [값]``` : 프로시저 내의 변수에 접근하여 값을 바꾼다.
- ```WHILE [조건] DO END WHILE``` : 프로시저 내에서 반복문을 수행한다.

예를 들어 10개의 데이터를 삽입한다면, 아래와 같이 구현할 수 있다.  

{% highlight sql %}
DELIMITER $$
DROP PROCEDURE IF EXISTS insertLoop$$

CREATE PROCEDURE insertLoop()
BEGIN
  DECLARE i INT DEFAULT 1;
  WHILE i <= 10 DO
    INSERT INTO test_board(id, title, author)
    VALUES (i, concat('HELLO ', i), concat('KIM ', i));
    SET i = i + 1;
  END WHILE;
END$$
DELIMITER $$

CALL insertLoop; $$
{% endhighlight %}  

<img width="506" alt="스크린샷 2024-03-17 오후 4 56 30" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/4b5897df-f0e1-4bf0-97c2-260b1cf8043e">  

아래와 같이 조회해보면, 프로시저가 잘 생성되었음을 확인할 수 있다.  
또한, 실행 시 더미 데이터가 잘 삽입되었음을 확인할 수 있다.  

<img width="430" alt="스크린샷 2024-03-17 오후 5 07 54" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/f4a1db40-a0e9-4032-8628-6a2c89a24957">
<img width="399" alt="스크린샷 2024-03-17 오후 4 57 10" src="https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/bdf92193-ac40-4902-a591-d2df2457d73f">
