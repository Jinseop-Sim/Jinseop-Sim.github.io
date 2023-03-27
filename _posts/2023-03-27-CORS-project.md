---
layout: post
title: "CORS의 실제 적용"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
예전에 CORS가 뭔지에 대해서만 잠깐 글을 썼었다.  
그 이후에 대충 검색을 해, 나오는 코드를 따라서 유사하게 만들어 놓았는데,  
어느 순간 또 ```preflight CORS``` 에러가 발생하기 시작했다.  

너무 생각 없이 적용만 해 놓았다는 생각이 문득 들었다.  
따라서 이번 기회에 제대로 알고 제대로 사용해보려고 한다.  

### CORS란?
아래의 페이지에서 이전에 정리 했었다.  
[this.(CORS? SOP?)](https://jinseop-sim.github.io/tech/2022/12/27/CORS.html)  

### Spring 실전 적용
Spring에서 CORS 오류를 해결하기 위해 아래와 같은 방안들을 지원한다.  

1. ```@CrossOrigin```
해당 방식은 Controller에 Annotation을 붙여서 사용할 수 있다.  
그럼 해당 Controller의 URL들은 모두 해당 Origin을 허가한다.

2. ```WebConfig```의 ```addCorsMappings```
해당 방식은 Configuration에서 따로 도메인을 지정한다.  
즉, 특정 도메인의 요청을 모두 허가할 때 사용할 수 있다.  
굳이 ```@CrossOrigin```을 쓰지 않아도 모두 허가되는 것이다.  
한 기능이 아닌 전체를 허가하므로, 좀 더 편한 방법이라고 볼 수 있다.  

3. Proxy의 생성
위의 방식들과는 전혀 다른 방식이다.  
위의 방식들은 Server단에서 무언가 제한을 풀어 허가를 했다면,  
이번에는 Client단에서 애초에 요청을 우회해서 보내는 방식이다.  

__Proxy__ 란, __가짜__ 의 의미이다.  