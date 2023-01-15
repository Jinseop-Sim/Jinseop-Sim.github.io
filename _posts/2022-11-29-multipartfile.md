---
layout: post
title: "AWS S3 & Multipart"
categories: ToyProject
tags: [devops]
author:
  - Jinseop Sim
toc: true
---
### S3?
AWS에는 S3이라는 Bucket이 존재한다.  
이는 Simple Storage Service의 약자로, AWS에서 제공하는 클라우드 저장소이다.  
웹 사이트, 모바일 App, 아카이브 등 여러 데이터를 저장하고 보호받을 수 있다.  
간단하게 말했지만, 매우 많은 기능을 제공하는 저장소이다.  

나는 이번 프로젝트를 진행하면서, 사진을 업로드하는 저장소로 사용했다.  
프로필 사진이나 게시글의 사진을 저장소에 넣고, 조회 시 렌더링하도록 하는 것이다.

그런데 뭔가 이상하다는 생각이 들 것이다.  
분명히 게시글이나 회원 정보는 ```String``` 타입의 글인데,  
이미지 파일은 도대체 어떤 식으로 넘겨 받아야 할까?  
Springboot에서는 ```MultipartFile``` 이라는 아주 간편한 타입을 제공한다.  

### Multipart File?
Multipart File에 설명하기에 앞서, Multipart에 대해서 알아보자.  
__Multipart__ 란 HTTP의 Data 형식 중 하나로, 특이한 형태의 Data이다.  

일반적으로 Form Data를 전송하면, ```application/x-www-form-urlencoded```의 형식으로 전송된다.  
하지만 이는, HTTP Body에 원하는 Data가 ```Key-Value``` 값으로 문자 Data가 전송될 뿐이다.  
그럼 이미지 파일과 같은 Binary 형태의 파일 데이터를 어떻게 같이 보낼 수 있을까?  
바로 ```multipart/form-data``` 형식으로 파일을 전송하는 것이다.  

해당 형식으로 ```Content-Type```을 정하게 되면, 요청의 형태가 달라진다.  
```Boundary```라는 개념으로 Data를 여러가지 Part로 나누어 요청을 보내게 된다.  