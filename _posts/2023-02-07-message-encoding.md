---
layout: post
title: "쪽지 기능과 한글 Encoding"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
쪽지 기능을 거의 다 개발했을 무렵, 마지막 난관에 봉착했다.  
아래와 같이 한글로 된 Message를 보내면 500 에러가 발생했다.  
<img width="444" alt="한글 에러" src="https://user-images.githubusercontent.com/71700079/217264245-2279d50d-8a46-4ecb-8981-b150599ac7cc.png">  

확인을 해보니 아래와 같이 부정확한 String 값이 들어왔다고 한다.  
이는 필시 한글을 썼기 때문에 발생하는 에러인데, 해결을 해보자.  
<img width="1336" alt="한글 에러 2" src="https://user-images.githubusercontent.com/71700079/217264257-9715ec5c-946f-4f51-a6fd-d1f2bf2400fe.png">  

에러 메시지를 보면 ```SQLSyntaxException``` 임을 알 수 있다.  
이는 즉, 내 DB에 값을 넣을 때 한글 인코딩이 현재 불가능 하다는 것이다.  
현재 AWS RDS를 사용하는 나는, 가서 인코딩을 먼저 손보기로 했다.  
이전에 __Timezone__ 을 건드렸던 Parameter group을 손보면 될 것이다.  

확인해보니 Charset은 이미 설정되어 있었다.  
하지만 __Collation__ 이 기본값에 한글 설정이 안되어있음을 볼 수 있다.  
![collation](https://user-images.githubusercontent.com/71700079/217264463-5a896a09-83fc-4d58-a39b-2d7e65c8bea8.png)  

Charset과 Collation은 각각 어떤 의미를 가지는가?  
- Charset : Character를 표현할 심볼과 표현 방식.
- Collation : Charset으로 구성된 문자열을 비교하는 방식.
  - 즉, 정렬에 사용되는 방식을 의미한다.
![colla2](https://user-images.githubusercontent.com/71700079/217264346-ae2e1a3b-e7ae-4d3d-b530-bc535d145982.png)

Collation을 설정하지 않아 String이 깨졌던 것으로 판단되어 수정했다.  
![colla2](https://user-images.githubusercontent.com/71700079/217264499-75cbbeb2-d4e9-43da-a73f-672629fecaab.png)  

하지만 Collation을 설정해도 여전히 문제는 그대로 였다.  
그래서 Query Console을 통해 DB Charset을 확인해보았다.  
<img width="978" alt="라틴" src="https://user-images.githubusercontent.com/71700079/217264514-6cebf94c-8a8f-402e-871e-9ee03aa48ed0.png">  
위와 같이 Charset과 Collation이 Latin으로 설정되어 있음을 볼 수 있다.  
검색을 해보니, 파라미터 그룹을 설정한 뒤 DB를 재 생성하면 된다는 말이 나왔다.  
하지만 나는 그냥 Query를 통해 해당 값을 수정하기로 했다.  
<img width="832" alt="변경쿼리" src="https://user-images.githubusercontent.com/71700079/217264560-32474e4f-980a-4683-8c2a-26a1879749a5.png">  
<img width="848" alt="변경확인" src="https://user-images.githubusercontent.com/71700079/217264620-6bee572d-e53b-4b08-8e24-bea06691da79.png">  

수정을 하고 나니 아래와 같이 깔끔하게 쪽지가 보내짐을 볼 수 있다!  
<img width="455" alt="성공'" src="https://user-images.githubusercontent.com/71700079/217264650-844e6ec7-59a0-4f2f-a7a6-acf78f5ac0b9.png">  
