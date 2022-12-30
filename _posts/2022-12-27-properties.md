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
어떤 모니터링 시스템을 구축했길래 이것이 가능할까?  

### application.yml