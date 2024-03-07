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
 	graph[to].push_back(from);
}
{% endhighlight %}  

예시 문제를 보면, ```1번``` 노드를 들렀다가 오른쪽으로 가는 걸 볼 수 있다.  
이 때 모든 양을 다 수집한 뒤, 다시 ```4번``` 노드로 이동을 해야 하는데,  
이 경우는 이미 방문했던 ```1번``` 노드로 다시 들어가는 상황이 된다.  
이런 경우는 어떻게 구현을 해야하는 지가 관건일 것 같다.  

나는 방문을 확인하는 배열을 2차원 배열로 만들어 시도했다.  
또한 양과 늑대는 한 번 데려가면 없어지므로, 해당 체크 배열도 만들었다.  
아래와 같이 구현하였다.  
{% highlight cpp %}
void backtrack(vector<int> info, int curr_node, int sheep_count, int wolf_count) {
	if (sheep_count == wolf_count) {
		// 같아지는 경우는 더 이상 못감
		return;
	}

	if (curr_node == 0) {
		// 다시 루트 노드로 돌아온 경우
		cout << sheep_count << '\n';
	}

	for (int i = 0; i < graph[curr_node].size(); i++) {
		int next_node = graph[curr_node][i];

		if (!visit[curr_node][next_node]) {
			visit[curr_node][next_node] = true;

			if (info[next_node] && !sheep_wolf_visit[next_node]) {
				sheep_wolf_visit[next_node] = true;
				backtrack(info, next_node, sheep_count + 1, wolf_count);
			}

			if (!info[next_node] && !sheep_wolf_visit[next_node]) {
				sheep_wolf_visit[next_node] = true;
				backtrack(info, next_node, sheep_count, wolf_count + 1);
			}

			if (sheep_wolf_visit[next_node])
				backtrack(info, next_node, sheep_count, wolf_count);

			visit[curr_node][next_node] = false;
		}
	}
}
{% endhighlight %}  

하지만 이 로직에는 문제가 굉장히 많다.  
방문했던 곳을 다시 방문하는 동작 자체가 불가능했다.  
결국 조금 더 고민을 하다 다른 사람들의 풀이를 참고했다.  

### 백트래킹 + Queue
굉장히 특이하게 백트래킹에 ```Queue```를 이용하고 있었다.  
갈 수 있는 노드들만 순간순간 판단하여 탐색을 하는 것이다.  
아래와 같이 백트래킹 로직이 구현되어 있다.  
{% highlight cpp %}
void backtrack(vector<int> info, int curr_node, int sheep_count, int wolf_count, queue<int> node_q) {
	if (!info[curr_node])
		sheep_count++;
	else
		wolf_count++;

	cout << curr_node << " " << sheep_count << " " << wolf_count << '\n';
	if (wolf_count >= sheep_count)
		return;
	result = max(sheep_count, result);

	for (int i = 0; i < graph[curr_node].size(); i++)
		node_q.push(graph[curr_node][i]);
	// 갈 수 있는 노드를 모두 큐에 저장한다.
	for (int i = 0; i < node_q.size(); i++) {
		int next_node = node_q.front();
		node_q.pop();
		cout << i << '\n';
		backtrack(info, next_node, sheep_count, wolf_count, node_q);

		node_q.push(next_node);
	}
}
{% endhighlight %}  

현재 노드에서 갈 수 있는 모든 노드를 큐에 넣어 유지시킨다.  
반대편 노드로 갔다가 현재 노드로 돌아오는 동작이 가능해지는 것이다.  
또한 조건에 의해 함수가 종료되었을 때, 다시 큐에 현재 노드를 집어넣는다.  
이는 ```반대편 노드에서 더 진행을 한 뒤 다시 돌아왔을 때를 탐색하기 위함```이다.  

위의 로직으로는 반대편 노드를 탐색하고 현재 노드로 돌아오는 움직임이 가능하다.  
어차피 돌아오는 과정에 ```root``` 노드를 거치게 되니, 정답이 되는 셈이다.  
어떻게 이런 아이디어가 떠오르는 지 경이로울 따름이다.  

### 백트래킹 + 3차원 배열
다른 풀이로는, 내가 생각했던 풀이와 유사한 풀이가 있었다.  
나는 2차원의 방문 배열을 사용했지만, 3차원을 사용한 풀이였다.  
이 풀이가 내가 원했던 직관적인 풀이대로 잘 풀어주었다고 생각한다.  
아래와 같이 함수가 구현되어 있다.  
{% highlight cpp %}
void backtrack(vector<int> &info, int curr_node, int sheep_count, int wolf_count) {
	if (curr_node == 0)
		result = max(result, sheep_count);
	// 루트로 돌아오는 경우에 최대값을 갱신

	for (int i = 0; i < graph[curr_node].size(); i++) {
		int next_node = graph[curr_node][i];
		if (!info[next_node]) {
			// 다음 노드가 양인 경우
			if (!visit[next_node][sheep_count + 1][wolf_count]) {
				visit[next_node][sheep_count + 1][wolf_count] = true;
				info[next_node] = VACANT;
				backtrack(info, next_node, sheep_count + 1, wolf_count);
				visit[next_node][sheep_count + 1][wolf_count] = false;
				info[next_node] = 0;
			}
		}
		else if (info[next_node] == 1) {
			// 다음 노드가 늑대인 경우
			if (!visit[next_node][sheep_count][wolf_count + 1]) {
				if (sheep_count == wolf_count + 1)
					continue;
				// 늑대를 획득했을 때 양과 같으면 다음 노드 탐색

				visit[next_node][sheep_count][wolf_count + 1] = true;
				info[next_node] = VACANT;
				backtrack(info, next_node, sheep_count, wolf_count + 1);
				visit[next_node][sheep_count][wolf_count + 1] = false;
				info[next_node] = 1;
			}
		}
		else {
			// 다음 노드가 빈 노드일 경우
			if (!visit[next_node][sheep_count][wolf_count]) {
				visit[next_node][sheep_count][wolf_count] = true;
				backtrack(info, next_node, sheep_count, wolf_count);
				visit[next_node][sheep_count][wolf_count] = false;
			}
		}
	}
}
{% endhighlight %}  

```visit[x][y][z]```에 대해서 각각의 칸은 다음을 의미한다.  
- ```x``` : 현재 노드의 번호
- ```y``` : 현재 획득한 양의 수
- ```z``` : 현재 획득한 늑대의 수

즉 ```x```번 노드에 ```y, z```마리의 동물을 데리고 온 적이 있음을 판단한다.  
이미 해당 정보대로 데리고 온 적이 있다면, 방문할 필요가 없다.  

이제 탐색하며 다음 노드가 양인지 늑대인지에 따라 분기를 나눈다.  
- 양 : 양을 하나 획득하고 진행한다.
- 늑대 : 늑대의 수가 양의 수와 같아지면 중지한다.

완전 직관적인 백트래킹 풀이이다.  

어떤 알고리즘이 사용되는 지 판단하기는 쉬운 문제였다고 생각한다.  
하지만 디테일한 아이디어가 떠올리기 굉장히 어려웠던 문제같다.  
조금 더 많이 풀어 사고력을 길러보자.  
