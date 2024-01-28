---
layout: post
title: "프로그래머스 Level 2 - 도넛과 막대 그래프"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [도넛과 막대 그래프[LEVEL 2]](https://school.programmers.co.kr/learn/courses/30/lessons/258711)  

이번 문제는 프로그래머스의 레벨 2 문제, 도넛... 문제이다.  
이 문제는 카카오 2024 인턴십 코딩테스트 문제였다.  
그 때 당시에는 이렇게 저렇게 풀다 결국 못 푼 문제였다.  

그래프의 간선들이 주어지고, 그래프의 형태를 판단하는 문제다.  
최대 정점의 수가 주어지지 않기 때문에, 최대 정점을 구해야 한다.  
그래프가 아닌 생성된 정점을 찾고, 각 그래프의 수를 구해야 한다!  

### 생성된 정점
생성된 정점이란 주어진 모양의 그래프를 제외한,  
나머지 그래프들을 모두 연결하고 있는 하나의 정점을 의미한다.  
해당 정점을 구하는 것도 답에 포함되니, 어떻게 구할 수 있을까?  

이는 굉장히 간단하다.  
문제의 그래프를 잠깐 살펴보기만 해도 알 수 있다.  

![image](https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/509acd72-714a-4141-a05d-ea5a6ec8ca96)  

이 정점은 그래프의 일부가 아니니, 들어오는 간선이 없다.  
즉, 진출차수(outdegree)만 존재하고 진입차수(indegree)가 없다.  

그래프 내에도 그런 정점이 있을 수 있지 않을까?  
물론 막대 그래프 같은 경우에, 시작 노드가 동일한 특성을 갖는다.  
하지만 해당 정점은 진출차수가 1이라는 점이 다르다.  

이 문제는 그래프의 수의 합이 2 이상이라는 전제 조건이 주어졌다.  
따라서 생성된 정점은 진출차수가 2 이상일 수 밖에 없다!  

### DFS
생성된 정점을 구했으니, 이제 그래프를 어떻게 판단할지가 문제이다.  
각 그래프의 특징을 잘 한 번 생각해보면, 아래와 같은 특징을 갖는다.  

- 막대 그래프
  - 사이클이 존재하지 않는다.
  - 즉, 시작 노드가 다시 등장하지 않는다.
- 도넛 그래프
  - 사이클이 존재하므로, 시작 노드가 다시 등장한다.
  - 단, 시작 노드는 항상 마지막에 다시 등장한다.
- 8자 그래프
  - 사이클이 존재하므로, 시작 노드가 다시 등장한다.
  - 단, 시작 노드는 그래프 탐색 중간에 다시 등장한다.
 
위와 같은 특징을 검사하기 위해, 그래프를 탐색했다.  
생성된 정점을 그래프에서 삭제한 뒤, DFS로 탐색하려 했다.  
하지만 문제는 그래프를 어떤 방식으로 탐색할 지가 문제였다.  

나는 인접리스트 형식으로 아래와 같이 그래프를 생성했다.  
{% highlight cpp %}
vector<int> graph[1000001];
int last_node = 0;
for (int i = 0; i < edges.size(); i++) {
	int from = edges[i][0];
	int to = edges[i][1];
	last_node = max(max(last_node, from), to);

	graph[from].push_back(to);
	node_checker[from].first++; // 나가는 거
	node_checker[to].second++; // 들어오는 거
}
{% endhighlight %}  

그래서 처음에는 1부터 ```last_node```까지 모두 훑으며 탐색하려 했다.  
그랬더니 막대 그래프를 탐색할 때 문제가 발생했다.  

예를 들어 ```4-2-3-5```와 같은 막대 그래프가 존재한다고 가정해보자.  
그런데 나는 1부터 끝까지 탐색을 하니, ```2```번 노드로 먼저 진입할 것이다.  
그럼 ```2-3-5```는 훑고 방문처리가 되고, 막대 그래프의 수가 + 1 될 것이다.  

여기서 이제 4번 노드를 탐색을 하게 된다면 어떻게 될까?  
사실 노드 1개만 있는 경우도 막대 그래프이므로, 막대 그래프 수 + 1이 되어 버린다.  
하나의 막대 그래프인데, 두 개로 인지해버려 답이 틀리게 되는 것이다.  
이렇게 그래프를 탐색하는 것은 옳지 않은 방식인 것 같다.  

### 진입 & 진출 차수
모든 경우의 수를 다 따져보니, 그래프 탐색만으로는 풀 수 없다.  
완전히 모든 경우를 셀 수 있다고 생각했으나,  
도넛 모양의 그래프와 8자 그래프에서 겹치는 경우가 생겼다.  

도넛 모양 그래프만 시작한 곳과 같은 데서 끝난다고 생각했는데,  
8자 모양 그래프에도 마찬가지로 그런 경우가 있었다.  
즉, 도넛 모양과 8자 모양을 구분할 수가 없다.  

그래서 결국 다른 사람의 풀이를 참고했다.  
다른 사람들의 풀이는 그래프 탐색을 하지 않는 풀이도 있었다.  
해당 풀이를 참고해서 진입 진출 차수를 통해 문제를 풀었다.  

진입 차수와 진출 차수를 저장하는 배열을 만들어 각각 저장한다.  
먼저, 생성된 정점은 어떤 특징을 가지는가?  
진입 차수가 없으며, 진출 차수가 반드시 1개 이상인 정점이다.  
단순히 분기문만으로 판단이 가능하다.  

이후 각 그래프가 가지는 특징에 따라 모양을 판별한다.  

- 막대 그래프
  - 막대 그래프의 리프 노드는 진출 차수가 0이다.
  - 이 경우는 유일하다.
- 8자 그래프
  - 8자 그래프에는 반드시 진입 차수가 2 이상, 진출 차수가 2인 정점이 있다.
  - 진입 차수가 2 이상인 이유는, 생성된 정점에 꽂힌 정점일 수 있기 때문이다.
- 도넛 그래프
  - ```생성된 정점의 진출 차수 - (막대 수 + 도넛 수)```가 된다.
  - 생성된 정점의 진출 차수가 곧 전체 그래프 수이기 때문이다.

최종적으로 아래와 같이 구현하였다.  
{% highlight cpp %}
int indegree[1000001], outdegree[1000001];
int check_created_node(int last_node) {
	for (int i = 1; i <= last_node; i++)
		if (indegree[i] == 0 && outdegree[i] > 1)
			return i;
}

bool check_bar_graph(int target) {
	if (outdegree[target] == 0)
		return true;
	return false;
}

bool check_eight_graph(int target) {
	if (indegree[target] >= 2 && outdegree[target] == 2)
		return true;
	return false;
}

vector<int> solution(vector<vector<int>> edges) {
	vector<int> answer(4, 0);

	int last_node = 0;
	for (int i = 0; i < edges.size(); i++) {
		int from = edges[i][0];
		int to = edges[i][1];
		last_node = max(max(last_node, from), to);

		indegree[to]++; // 해당 노드 진입 차수
		outdegree[from]++; // 해당 노드 진출 차수
	}

	int created_node = check_created_node(last_node);
	answer[0] = created_node;

	for (int i = 1; i <= last_node; i++) {
		if (i == created_node)
			continue;

		if (check_bar_graph(i))
			answer[2]++;
		if (check_eight_graph(i))
			answer[3]++;

		answer[1] = outdegree[created_node] - (answer[2] + answer[3]);
	}

	return answer;
}
{% endhighlight %}
