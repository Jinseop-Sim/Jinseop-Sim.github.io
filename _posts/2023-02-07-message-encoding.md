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
[사진 첨부]  

확인을 해보니 아래와 같이 부정확한 String 값이 들어왔다고 한다.  
이는 필시 한글을 썼기 때문에 발생하는 에러인데, 해결을 해보자.  

에러 메시지를 보면 ```SQLSyntaxException``` 임을 알 수 있다.  
이는 즉, 내 DB에 값을 넣을 때 한글 인코딩이 현재 불가능 하다는 것이다.  
현재 AWS RDS를 사용하는 나는, 가서 인코딩을 먼저 손보기로 했다.  
이전에 __Timezone__ 을 건드렸던 Parameter group을 손보면 될 것이다.  

아래와 같이 Charset은 이미 설정되어 있으니 넘어가자.  
__Collation__ 이 기본값에 한글 설정이 안되어있음을 볼 수 있다.  

Charset과 Collation은 각각 어떤 의미를 가지는가?  
- Charset : Character를 표현할 심볼과 표현 방식.
- Collation : Charset으로 구성된 문자열을 비교하는 방식.
  - 즉, 정렬에 사용되는 방식을 의미한다.

Collation을 설정하지 않아 String이 깨졌던 것으로 판단되어 수정했다.  
하지만 Collation을 설정해도 여전히 문제는 그대로 였다.  
그래서 Query Console을 통해 DB Charset을 확인해보았다.  

위와 같이 Charset과 Collation이 Latin으로 설정되어 있음을 볼 수 있다.  
검색을 해보니, 파라미터 그룹을 설정한 뒤 DB를 재 생성하면 된다는 말이 나왔다.  
하지만 나는 그냥 Query를 통해 해당 값을 수정하기로 했다.  

수정을 하고 나니 아래와 같이 깔끔하게 쪽지가 보내짐을 볼 수 있다!  