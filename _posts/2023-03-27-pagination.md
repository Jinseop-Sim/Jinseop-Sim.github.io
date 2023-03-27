---
layout: post
title: "Pagination에 대한 짧은 생각"
categories: ToyProject
tags: [develop]
author:
  - Jinseop Sim
---
현재 진행 중인 프로젝트에는 게시판에 Pagination 기능이 들어간다.  
나름 기본적으로 구현해야 할 기능 중 하나라고 생각한다.  

처음엔 Pagination의 성능에 대해서 생각했다.  
- 만약 Pagination 로직을 Backend에 구현해놓는다면?
  - Frontend는 버튼만 만들어 요청을 보내기만 하면 된다.
  - 단, 페이지를 넘길 때 마다 매번 Query가 날아간다.
    - 즉, 사용자가 증가했을 때 조회 시 마다 많은 Query가 날아간다.
    - 이는 성능에 전혀 문제가 없는가?
- 그럼 Frontend에 로직을 구현해놓는다면?
  - Frontend