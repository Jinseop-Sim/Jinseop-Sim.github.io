---
layout: post
title: "Linked List"
categories: Algorithm
tags: [cpp]
author:
  - Jinseop Sim
---

## Linked List
> 흔히 사용하는 자료구조 중 하나인 연결 리스트에 대해 알아보자.  

연결 리스트는 탐색이 주 목적일 때에는 사용하지 않는다.  
왜냐하면 Node를 옮겨 다니며 탐색을 하기엔 시간이 너무 오래 걸리기 때문이다.  

그럼 어떤 이유에서 사용을 하는가?  
- 중간에 있는 Node를 삭제하는 것이 목적일 때
- 중간에 Node를 삽입하는 것이 목적일 때
- 두 동작의 시간 복잡도는 O(1)이다.