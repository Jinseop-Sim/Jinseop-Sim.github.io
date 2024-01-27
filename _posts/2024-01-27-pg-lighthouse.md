---
layout: post
title: "프로그래머스 Level 3 - 등대"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [등대[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/133500)

이번 문제는 오랜만에 푸는 프로그래머스 문제이다.  
프로그래머스의 레벨 3 등대 문제이다.  
문제를 차근차근 한 번 읽어보도록 하자.  

최대 100000개의 등대가 주어지며, 모든 뱃길을 밝혀야 한다.  
이어진 두 등대 중 하나만 켜지면 해당 뱃길은 밝혀진 것으로 간주한다.  
이 때 최소한의 등대를 켜서 모든 뱃길을 밝혀라.  

### 정렬
처음 든 생각은 정렬을 한 뒤, 그래프를 탐색한 것이다.  
최소한으로 등대를 켜서 모든 뱃길을 밝히는 것이 문제였다.  
그럼 최대한 등대가 많이 연결된 등대를 밝히는 게 이득 아닐까?  
즉, Greedy하게 풀 수 있는 방법을 생각해보았다.  

우선 주어진 뱃길의 입력을 그래프의 형태로 가공한다.  
이후, 연결된 등대가 많은 순서대로 그래프를 정렬해본다.  
이제 그래프를 탐색하면 되는데, 이 때 문제가 있다.  
연결된 등대의 수가 같을 때, 즉 Tie break가 문제가 된다.  

![image](https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/973a09ca-f35f-4536-937c-aa96534e544c)   

만약 위의 예시에서 9번 등대 대신 2번 등대를 먼저 킨다면?  
정답은 3개지만, 나는 4개로 판단하게 될 것이다.  
그럼 켤 수 있는 등대는 미리 켰다고 표시를 하도록 하면?  

![image](https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/5d675dd0-6d5f-4cbc-8ee5-1cb2de24efb3)  

그럼 위의 예시에서 1번을 켰을 때, 5번도 켜져버리게 된다.  
그럼 우리는 5번 등대를 탐색하지 않으므로 답을 구할 수 없다.  

이 문제를 Greedy하게 푸는 것은 분기 과정에서 문제가 있다.  
다른 방법을 한 번 생각해보도록 하자.  

### 위상 정렬
문제를 보다 보니 위상 정렬로도 풀 수 있지 않을까? 하는 생각이 들었다.  

우리는 뱃길 ```A - B```에 대해서 둘 중 하나의 등대는 반드시 켜야한다.  
즉, 트리를 탐색해 나갈 때 둘 중 하나는 키면서 지나가야 한다는 것이다.  
이 때 그럼 어느 등대를 키는 게 이득일까?  
리프 노드부터 탐색을 진행한다면, 부모 노드를 키는게 당연히 이득이다.  
왜? 리프 노드는 연결된 등대가 하나 밖에 없기 때문이다.  
그 말은, 리프 노드부터 탐색하며 부모 노드를 키면서 지나가면 된다는 말이다!  

리프 노드부터 차례차례 탐색할 수 있는 방법이 뭐가 있을까?  
간단하게는 DFS로도 탐색할 수 있을텐데, 이는 리프 노드를 다 찾아야 한다.  
하지만 위상 정렬을 사용한다면 어떨까?  

리프 노드는 양방향 그래프에서 진입 차수가 반드시 1이다.  
이 점을 이용해서 아래와 같이 트리를 순회할 수 있을 것이다.  

![image](https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/03c7548d-8b2f-49a5-9483-ed35d04eebaa)  

현재 노드가 꺼져있으면, 현재 노드의 부모 노드를 켜준다.  
그럼 현재 노드가 켜져있으면? 그냥 지나가면 된다.  
그렇게 진행하면 위와 같이 최소한의 노드만 켜져있게 된다!  

전체적인 코드는 아래와 같이 구현되었다.  
{% highlight cpp %}
int indegree[100001];
vector<int> graph[100001];
bool visit[100001];
int topology_sort(int vertex) {
	int answer = 0;

	queue<int> top_q;
	for (int i = 1; i <= vertex; i++)
		if (indegree[i] == 1)
			top_q.push(i);
	// 진입 차수가 1이면 Leaf node.
	
	while (!top_q.empty()) {
		int curr_node = top_q.front();
		top_q.pop();

		for (int i = 0; i < graph[curr_node].size(); i++) {
			int next_node = graph[curr_node][i];
			indegree[next_node]--;

			if (indegree[next_node] == 1)
				top_q.push(next_node);
			if (visit[curr_node] == false)
				visit[next_node] = true;
			// 자식 노드가 꺼져있을 때, 부모노드를 켜는게 핵심이다.
		}
	}

	for (int i = 1; i <= vertex; i++)
		if (visit[i])
			answer++;

	cout << answer;
	return answer;
}

int solution(int n, vector<vector<int>> lighthouse) {
	for (int i = 0; i < lighthouse.size(); i++) {
		int from = lighthouse[i][0];
		int to = lighthouse[i][1];

		graph[from].push_back(to);
		graph[to].push_back(from);
		indegree[from]++;
		indegree[to]++; // 진입 차수
	}

	return topology_sort(n);
}
{% endhighlight %}

### Appendix : DFS
사실, DFS로 구현하는 방식은 잘 생각이 나지 않았는데,  
다른 사람들의 풀이를 참고해보니 모두 DFS로 푼 것 같았다.  

간단하게 DFS로 구현하는 방식은 아래와 같다.  
DFS 방식대로 트리를 쭉 내려가되, 다음 노드가 내가 온 노드인지 검사한다.  
이는 그래프가 양방향으로 주어졌기 때문에 검사해야 무한루프가 돌지 않는다.  

쭉 타고 내려가며, 현재 노드와 다음 노드가 둘 다 켜져있지 않다면?  
둘 중 하나는 반드시 켜져야 하므로, 위에서 말했듯 부모 노드를 켜준다.  
DFS에서는 현재 노드가 부모 노드가 되고, 다음 노드가 자식 노드이므로  
현재 노드를 켜주고 다음 노드로 DFS를 진행한다.  

{% highlight cpp %}
vector<int> graph[100001];
bool visit[100001];
int answer = 0;
void dfs(int curr_node, int parent) {
	for (int i = 0; i < graph[curr_node].size(); i++) {
		int next_node = graph[curr_node][i];

		if (next_node != parent) {
		// 다음 노드가 부모와 다른지 확인해야만 한다.
		// 그래야 거꾸로 돌아가지 않는다!
			dfs(next_node, curr_node);
			// 현재 노드를 부모로 하는 다음 노드로 진행.

			if (!visit[curr_node] && !visit[next_node]) {
				visit[curr_node] = true;
				// 다음 노드와 연결된 부모 노드인
				// 현재 노드를 키는게 이득이다!
				answer++;
			}
		}
	}
}

int solution(int n, vector<vector<int>> lighthouse) {
	for (int i = 0; i < lighthouse.size(); i++) {
		int from = lighthouse[i][0];
		int to = lighthouse[i][1];

		graph[from].push_back(to);
		graph[to].push_back(from);
	}

	dfs(1, 1);
	return answer;
}
{% endhighlight %}

원리는 위상 정렬과 같지만, 노드 방문 순서만 다르다!  
다양한 방식으로 문제에 접근하는 시각을 기르는 게 중요하다!  
