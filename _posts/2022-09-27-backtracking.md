---
layout: post
title: "Graph : Backtracking"
categories: Algorithm
tags: [cpp]
author:
  - Jinseop Sim
---
보통 그래프 문제를 푸는 방식은 DFS와 BFS로 나뉘게 된다.  
하지만 그 둘만으로는 풀리지 않는 문제가 있다. 그럴 떄에는 일종의 기술이 필요하다.  

만약 내가 지도를 들고 정글을 헤쳐나가고 있다고 생각해보자.  
지도 상으로는 지금 가는대로 간다면 막다른 길에 도달하게 된다.  
그래도 나는 앞으로 가서 막다른 길임을 두 눈으로 확인해야 하는가?  

상식적으로 생각해보면, 굳이 막다른 길임을 알았을 때는 다른 길로 다시 돌아가면 된다.  
이것을 알고리즘에서는 __Pruning(가지치기)__ 기법이라고 말한다.  
우리가 흔히 사용하던 DFS에 이 가지치기를 더한 것이 바로 Backtracking(되추적)이다!  

DFS로 깊이 우선 탐색을 진행하며 모든 경우를 다 뒤지는 것이 바보같은 행동인 경우가 있다.  

대표적인 문제로는 체스판에 퀸을 놓을 수 있는 가짓수를 세는 __N-Queens Problem__ 이 있으며,  
조합이나 순열을 구현할 때에도 Backtracking이 이용된다.  

### N-Queens Problem
{% highlight cpp %}

{% endhighlight %}