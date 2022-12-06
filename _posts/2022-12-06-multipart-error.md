---
layout: post
title: "[Debug] Multipart 사진 변환 오류"
categories: ToyProject
tags: [devops]
author:
  - Jinseop Sim
---
보통 웹 서비스에서 이미지를 게시하거나 프로필 사진 기능을 제공할 때,  
AWS의 __S3 Bucket__ 을 이용해서 이미지 파일을 관리한다고 한다.  

나도 공부를 하며, 다른 사람들이 만들어 놓은 Code를 참조해 아래와 같이 기능을 만들었다.  
- ```Service```  
<img width="723" alt="스크린샷 2022-12-06 오후 3 30 46" src="https://user-images.githubusercontent.com/71700079/205838378-946bea7a-8706-49c9-b024-d0a2c197d073.png">  
<img width="906" alt="스크린샷 2022-12-06 오후 3 30 57" src="https://user-images.githubusercontent.com/71700079/205838392-40e91a18-c1d7-4370-a80b-762c05f96ecf.png">  

- ```Configuration```  
<img width="965" alt="스크린샷 2022-12-06 오후 3 29 31" src="https://user-images.githubusercontent.com/71700079/205838353-a240e658-f2c2-42ee-aee0-5804423c588e.png">  

하지만 예기치 못하게, 계속 파일 변환에 실패를 하였다.  
```upload()``` 함수 내에 있는 ```convert()``` 함수에서 계속 오류가 터졌다.  

해당 함수는 다음과 같은 용도이다.  
- 내 Controller는 Image를 ```Multiparfile``` 형식으로 받는다.
- 하지만 S3에 Upload 되어야 하는 형식은 ```File``` Class의 형식이다.
  - 따라서, ```convert()``` 함수를 통해 ```File``` Class로 형변환을 한다.  

하지만, 함수는 제대로 작동하지 않았다.  
계속 검색도 해보고 찾아봤지만 해결법이 보이지 않던 중,  
File Class의 ```createNewFile()``` method와 함께 Local File이 눈에 들어왔다.  

먼저 해당 S3Uploader의 동작 방식은 아래와 같다.
1. 넘겨받은 Multipart Image를 Local 환경에 저장한다.
2. 해당 Image를 File로 변환하고, S3 Bucket에 Upload한다.
3. Local 환경의 File을 삭제한다.  

```createNewFile()```은 다음과 같은 기능을 한다.
- Type : boolean
- 해당 File이 이미 존재하면 False 반환
- 해당 File이 존재하지 않으면 True 반환

나의 경우에는 Local 환경에 이미 같은 파일이 저장되어 있어서,  
해당 함수가 계속 False를 반환했던 것이다. 간단하지만 바보같은 오류였다.  
