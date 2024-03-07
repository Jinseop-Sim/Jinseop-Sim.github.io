---
layout: post
title: "프로그래머스 Level 3 - 양과 늑대"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [양과 늑대 [LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/92343)  

이번 문제는 프로그래머스의 레벨 3 양과 늑대 문제이다.  
2022 KAKAO 공채 문제였다고 한다.  
문제를 차근차근 읽어보자.  

양과 늑대가 들어있는 이진트리가 문제로 주어진다.  
이 때 트리의 노드는 최대 ```17```개 까지 주어질 수 있으며,  
```1```과 ```0```으로 각각 ```양과 늑대```를 표현하도록 한다.  
간선의 수는 최대 ```노드 - 1```개 만큼 주어질 수 있다.  

이제 트리를 순회하며 양을 모으도록 한다.  
단, 늑대의 수가 양의 수와 같거나 많아지면 즉시 모두 잡아먹힌다.  
잡아먹히지 않고, 최대로 모을 수 있는 양의 수를 구하라!  

### 백트래킹
보자마자 백트래킹으로 풀 수 있지 않을까? 하는 생각이 들었다.  
양과 늑대의 수를 매개변수로 넘기며, 재귀적으로 진행하는 것이다.  

간선의 정보가 나와있으니 우리는 루트부터 트리를 순회할 수 있다.  
단, 양의 수와 늑대의 수가 같아지는 순간 종료를 하는 것이다.  

아래와 같이 그래프를 초기화 하며 곰곰히 생각해보니 문제가 있다.  
종료 조건을 만나 다시 돌아나왔을 때, 해당 경로로 다시 가야하는 경우는 어떡할까?  
{% highlight cpp %}
vector<int> graph[17];
for (int i = 0; i < edges.size(); i++) {
	int from = edges[i][0];
	int to = edges[i][1];

	graph[from].push_back(to);
}
{% endhighlight %}  

예시 문제를 보면, ```1번``` 노드를 들렀다가 오른쪽으로 가는 걸 볼 수 있다.  
이 때 모든 양을 다 수집한 뒤, 다시 ```4번``` 노드로 이동을 해야 하는데,  
이 경우는 이미 방문했던 ```1번``` 노드로 다시 들어가는 상황이 된다.  
이런 경우는 어떻게 구현을 해야하는 지가 관건일 것 같다.  