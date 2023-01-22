---
layout: post
title: "Application File의 민감정보"
categories: ToyProject
tags: [devops]
author:
  - Jinseop Sim
---
프로젝트를 진행하던 중에, AWS로 부터 당황스러운 메일을 받게 되었다.  
당신의 Git Repo에 __IAM Access Key__ 가 노출이 되었다는 메일이었다.  

내가 작성한 Code를 저장소에 Push한 결과물에 ```application.yml``` 파일에 문제가 있었다.  
Access Key와 DB 계정 정보 모두가 노출이 되어 올라간 것이다.  

당연히 보안에 문제가 발생할 가능성이 농후하기 때문에 이것은 잘못된 행동이다.  
더 놀랐던건, AWS가 이것을 12시간 안에 감지를 해 알려준다는 점이었다.  
어떤 모니터링 시스템을 구축했길래 이것이 가능할까? 새삼 놀랍다.  

아무튼 이런 일을 미연에 방지하기 위해서, 방안이 필요했다.  
처음엔 매번 ```####``` 이런 식으로 가려서 Push를 하려고 했으나,  
너무 번거롭고 좋지 않은 방법이라고 생각했다.  
그래서 검색을 좀 해보니 아래와 같은 방식을 이용하기로 했다.  

### application-secret.yml
본래 application.yml은 그대로 두고, 새 파일을 만들어 참조하는 방식이다.  
해당 ```secret.yml```은 문제가 될 만한 민감 정보들을 모두 저장해둔다.  
원래와 같이 ```spring.datasource.username=``` 과 같은 경로의 형태로 저장하면 된다.  

![image](https://user-images.githubusercontent.com/71700079/210355019-40d50887-425b-4848-adaa-39ca1c5ee75d.png)  
위와 같이 ```spring.profiles.include=...```를 이용하여 참조하도록 한다.  

당연히 해당 민감 정보 File은 ```.gitignore```에 등록해 Push 되지 않도록 한다.  

> __여기부터 2023.01.22 추가__
### gitignore의 재적용
처음에 위의 방식을 통해 적용했을 때는, ```.gitignore```이 동작하지 않았다.  
그대로 저장소에 ```application-secret.yml``` 이 올라가버려서 또 큰일이 날 뻔했다.  

그래서, 방법이 없을까 물색하던 도중 ```git rm -r --cahced``` 라는 명령어를 알게 되었다.  