---
layout: post
title: "������ MVC - Thymeleaf"
categories: Springboot
tags: [java]
author:
  - Jinseop Sim
toc: true
---

�츮�� MVC Pattern�� ���ؼ� ������ ��, ��û�� ���� ������  
��ȯ�� �� �ִ� 3���� ����� ���ؼ� �����.  

1. Static Resource
2. View Template (Dynamic View)
3. Message Body

�� �� ���� View�� ��ȯ�ϱ� ���ؼ� �츮�� JSP�� ����߾��µ�,  
���� ����� ���� Thymeleaf Engine�� ���ؼ� �������.  

### Thymeleaf Declare
```<html xmlns:th="http://www.thymeleaf.org">``` �� �����Ͽ� ����Ѵ�.  

### Thymeleaf�� �ٽ�
```th:xxx``` Tag�� �پ��ִ� �ڵ�� Server�ܿ��� �������� �ȴ�.  
������ HTML File�� ����, ```th:xxx``` Tag�� ���� �ڵ�� ���̰� �ȴ�.  

### Thymeleaf Link
```th:href = "@{/css/bootstrap.min.css}"```  

URL Link�� �ɾ ���� ���� ���, ```@{...}``` �� ����Ѵ�.  

### Thymeleaf Attribute
```<td><a href="item.html" th:href="@{/basic/item/{itemId}(itemId=${item.id})}"```  
�� �ڵ��, HTML�� �׳� ���� ```href```�� �̵�������,  
Server�ܿ��� �������� �ϴ� ��쿣, ```th:href```�� ������ �ȴ�.  
�̸� Ÿ�Ӹ������� __�Ӽ� ����__ �̶�� �Ѵ�.  
