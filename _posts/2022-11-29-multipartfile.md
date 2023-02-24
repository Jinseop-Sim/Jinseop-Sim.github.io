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
여기선 간단하게 말했지만, 매우 많은 기능을 제공하는 저장소이다.  

나는 이번 프로젝트를 진행하면서, 사진을 업로드하는 저장소로 사용하고,  
Backend, Frontend의 Build 파일을 배포하는 용도로도 사용할 것이다.  
사진이나 File Bucket에 업로드하면, __Endpoint__ 로 사진에 접근이 가능해진다.  

그런데 뭔가 조금 이상하다.  
분명히 게시글이나 회원 정보는 ```String``` 타입의 글인데,  
이미지 파일은 도대체 어떤 식으로 넘겨 받아야 할까?  
Springboot에서는 ```MultipartFile``` 이라는 아주 간편한 타입을 제공하고 있으며,  
```@RequestPart``` 라는 간편한 Annotation 또한 제공한다.  

아래에서 좀 더 자세하게 알아보자.  

### Multipart File?
Multipart File에 설명하기에 앞서, Multipart에 대해서 알아보자.  
우리는 HTTP Request를 보낼 때 ```content-type```을 ```MIME``` 형식으로 보낸다.  
> MIME(Multipurpose Internet Mail Extension), 전자 우편 인터넷 표준 포맷  

```MIME``` 형식으로 정해지는 ```content-type```은 여러가지가 존재하는데,  
HTML의 ```<form>```에서는 아래와 같은 Type들을 주로 지정한다.  
- ```text/plain``` : Text file의 기본 타입이다.
- ```application/x-www-form-urlencode``` : HTML Form의 기본 타입이다.
  - ```key=value&key=value``` 형태로 값이 전달된다.
- ```application/json``` : REST API에 많이 사용되는 Json 타입의 Content.
  - ```{key : value}``` 형태로 값이 전달된다.
- ```application/octet-stream``` : Binary file의 기본 타입이다.
- ```multipart/form-data``` : 대용량 Binary File 처리에 유용한 타입이다.

일반적으로 Form Data를 전송하면 ```application/x-www-form-urlencoded```의 형식을 사용하거나,  
RESTful API의 서비스라면, ```application/json```의 형식을 사용하는 경우가 대부분이다.  
하지만 이는, HTTP Body에 원하는 Data가 ```Key-Value``` 값으로 전송될 뿐이다.  
이미지 파일과 같은 Binary 형태의 파일 데이터는 어떻게 하나의 요청에 같이 보낼 수 있을까?  
바로 ```multipart/form-data``` 형식으로 파일을 전송하는 것이다.  

보통은 텍스트면 ```text/plain```, 이미지면 ```image/png```와 같이 하나의 타입으로 요청이 가는데,  
```multipart/form-data```로 타입을 정하게 되면, 요청의 형태가 달라진다.  
```Boundary```라는 개념으로 Data를 여러 Part로 나누어 요청을 보내게 된다.  
내가 보내려는 여러 Type의 Data를 하나처럼 연결해서 보내게 된다는 것이다.  

### Request Part?
위에서 말했듯이, Multipart는 여러 Part의 Data를 하나로 연결해서 보낸다고 했다.  
그렇다면 당연히 ```Controller```에서 받을 때에는 Part대로 분리해서 받아야 한다.  
이럴 때 ```@RequestPart``` Annotation을 통해서 Data를 받는다.  
즉 ```@RequestBody```가 필요하지만, Binary Stream이 포함된 경우에 사용할 수 있다.  

```@RequestBody```에서 ```HttpMessageConverter```가 Deserialization을 해주듯,  
```MultiPartResolver```가 동작해서 Deserialization을 해 Data를 Mapping 해준다고 한다.  

### 참고 문헌
> [Spring Docs : RequestPart](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/RequestPart.html)  
> [What is AWS S3](https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/userguide/Welcome.html)  