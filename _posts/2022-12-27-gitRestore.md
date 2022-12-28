---
layout: post
title: "Git Branch�� �Ǽ��� �������Ⱦ��!"
categories: ToyProject
tags: [devops]
author:
  - Jinseop Sim
---
������ ���ؼ��� �ݵ�� ��� �ϴ� SVN �� �ϳ��� Git.  
���� ���� ���� ������Ʈ������ ������ ���� Git�� ���� ����ϰ� �ִ�.  

������ ȥ�� �۾��� �ϴ� ū ������ �߻��� �� �߾���.  
�� ���� Branch�� �ĳ��� �۾��� �ϰ� �־��µ�, �ش� Branch�� Push�ϴ� �������� �浹�� �߻��ߴ�.  

������ �� ��Ȳ���� Push�� ���� �ʰ� Pull�� ���� �ʾƼ� ��Ȳ�ϰ� �Ǿ���.  
�ű�� ��û�ϰ� 

### Reflog
Git���� ```git reflog``` ��� ��ɾ �ִ�.  
�ش� ��ɾ�� ���� Git�� ```commit```�� �̷��� �� �� �ִ� ��ɾ��.  
�ش� �̷��� ```HEAD@{}``` ������ Hash ID�� ���� Commit�̳� Branch�� ������ �� �ִ�!  

- Commit ����
  - ```git reset --hard [Hash ID]```
- Branch ����
  - ```git checkout -b [Branch name] [Hash ID]```

���� ��ɾ ���� �����ϰ� ���� �̷����� ������ �����ߴ�.  
Git�� Ȯ���� ���� �ٷ�� �� ��ư� ������ �� ����.  