---
layout: post
title: "Git Branch를 실수로 날려버렸어요!"
categories: ToyProject
tags: [devops]
author:
  - Jinseop Sim
---
협업을 위해서는 반드시 써야 하는 SVN 중 하나인 Git.  
나는 현재 토이 프로젝트에서의 협업을 위해 Git을 적극 사용하고 있다.  

하지만 혼자 작업을 하다 큰 문제가 발생할 뻔 했었다.  
내 개인 Branch를 파놓고 작업을 하고 있었는데, 해당 Branch에 Push하는 과정에서 충돌이 발생했다.  

심지어 그 상황에서 Push도 되지 않고 Pull도 되지 않아서 당황하게 되었다.  
거기다 멍청하게 

### Reflog
Git에는 ```git reflog``` 라는 명령어가 있다.  
해당 명령어는 내가 Git에 ```commit```한 이력을 볼 수 있는 명령어다.  
해당 이력의 ```HEAD@{}``` 형태의 Hash ID를 통해 Commit이나 Branch를 복구할 수 있다!  

- Commit 복구
  - ```git reset --hard [Hash ID]```
- Branch 복구
  - ```git checkout -b [Branch name] [Hash ID]```

위의 명령어를 통해 안전하게 과거 이력으로 복구가 가능했다.  
Git은 확실히 아직 다루기 참 어렵고 복잡한 것 같다.  