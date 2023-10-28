---
layout: post
title: "Union Find & MST"
categories: Algorithm
tags: [cpp]
author:
  - Jinseop Sim
---
__Union Find__ 는 __Graph Algorithm__ 의 일종이다.  
그 의미는, __합집합 찾기__ 이며 __상호 배타적 집합(Disjoint-Set)__ 이라고 불리기도 한다.  

두 가지의 연산으로 구성된다.  
- Find : 어떤 Node x가 어떤 집합에 포함되어 있는지 찾는 연산
- Union : 어떤 두 Node x, y의 집합을 합치는 연산
  - 이는 곧, 두 Node를 연결한다는 의미와 같다.

## Disjoint Set과 Union find
1. 모두 연결되지 않고, 자신만을 원소로 가지는 집합이 있다.  
![unf1](https://user-images.githubusercontent.com/71700079/213406867-7a0c036e-a313-400c-9700-2e42a8963fea.png)  

2. ```Union(x, y)``` 연산을 통해 다음과 같이 연결할 수 있다.
  - 배열을 갱신하면, 해당 Node의 부모가 배열에 들어가게 된다.
  - 일반적으로 더 작은 값 쪽이 부모가 된다. 
![unf](https://user-images.githubusercontent.com/71700079/213406905-0dba012c-087e-47d2-9691-7558965a3b7a.png)  

3. ```Find(x)``` 연산을 통해 해당 Node의 부모를 알 수 있다.
  - Skewed Tree를 만들지 않기 위해, 인접 부모 노드가 아닌 Root 노드를 저장한다.  
![unf3](https://user-images.githubusercontent.com/71700079/213406949-f74a9205-d6d2-41f1-bbbb-cea6293d0824.png)  

### Example : 백준 1717 집합의 표현
{% highlight cpp %}
#include <iostream>
#include <vector>
using namespace std;

int nodes[1000001]; // 그래프
void init_nodes(int size) {
	for (int i = 1; i <= size; i++)
		nodes[i] = i;
}

int get_parent(int node) {
	// 인덱스와 자기 자신이 같으면 root node
	if (nodes[node] == node) return node;
	// 재귀적으로 부모(root)를 찾는다.
	return nodes[node] = get_parent(nodes[node]);
}

void do_union(int node_a, int node_b) {
	node_a = get_parent(node_a); // root 노드를 받아온다.
	node_b = get_parent(node_b);

	// 통상적으로 유니온 파인드는 작은 쪽이 부모가 된다.
	if (node_a > node_b) nodes[node_a] = node_b;
	else nodes[node_b] = node_a;
}

void find(int node_a, int node_b) {
	node_a = get_parent(node_a);
	node_b = get_parent(node_b);

	// 부모가 같음은, 같은 집합임을 의미한다.
	if (node_a == node_b) cout << "YES\n";
	else cout << "NO\n";
}

int main() {
	ios_base::sync_with_stdio(false);
	cin.tie(NULL);
	cout.tie(NULL);

	int num = 0, op_num = 0;
	int oper = 0, node_a = 0, node_b = 0;

	cin >> num >> op_num;
	init_nodes(num);

	for (int i = 0; i < op_num; i++) {
		cin >> oper >> node_a >> node_b;

		if (oper == 0)
			do_union(node_a, node_b);
		else
			find(node_a, node_b);
	}

	return 0;
}
{% endhighlight %}

> 참고 자료 출처 : [브랜든의 블로그](https://brenden.tistory.com/33)

## MST, 최소 신장 트리
Tree의 종류 중에 MST(Minimum Spanning Tree)라는 Tree가 있다.  
해당 Tree는 위에서 학습한 Union Find로 구현이 가능한 Tree이다.  

먼저 Spanning Tree부터 알아보자.  
- Spanning Tree : Cycle 없이 모든 Node를 이은 Tree.
  - 해당 Tree는 당연히 Link(Edge)의 수가 ```Node(Vertex) - 1```이 된다.

그럼 Minimum Spanning Tree란 뭘까?  
바로 Spanning Tree 중 가중치가 가장 작은 Tree를 의미한다.  
즉, 각 Edge에 가중치가 존재한다고 가정을 하고 구현하는 Tree이다.  

아래부터 MST를 구할 수 있는 알고리즘을 소개하도록 하겠다.  
## Kruskal Algorithm
MST를 구할 수 있는 첫번째 알고리즘, Kruskal 알고리즘이다.  
위에서 언급했듯이 ```Union Find```를 이용해 MST를 구할 수 있다고 했는데,  
바로 Kruskal Algorithm에 ```Union Find```가 사용된다.  

아래와 같은 순서로 진행된다.  
1. Graph의 모든 간선 정보를 빼내어 오름차순 정렬한다.

{% highlight cpp %}
for (int i = 0; i < ed; i++) {
	cin >> node_x >> node_y >> weight;
	graph.push_back({ weight, { node_x, node_y } });
}
{% endhighlight %}  

2. 비용이 가장 최소인 간선부터 두 노드를 연결해준다.
  - 해당 동작에 Union Find의 Union 동작이 들어간다.

{% highlight cpp %}
void do_union(int node_x, int node_y) {
	node_x = get_parent(node_x);
	node_y = get_parent(node_y);

	if (node_x < node_y) parent[node_x] = node_y;
	else parent[node_y] = node_x;
}
{% endhighlight %}  

3. 계속 그래프를 훑으며 Union을 반복한다.
  - 이 때, ```Union Find```의 특징적인 동작이 보인다.
  - 반복시에 Cycle이 발생하면 MST에 포함시키지 않는다.
    - 이는 ```Union Find```의 ```find()```를 이용하는 방식이다.
    - 두 노드의 부모가 같으면, Cycle의 발생을 의미한다!

{% highlight cpp %}
bool checkCycle(int node_x, int node_y) {
	node_x = get_parent(node_x);
	node_y = get_parent(node_y);

	if (node_x == node_y)
		return true;
	return false;
}
{% endhighlight %}  

4. 모든 배열을 다 순회하고 나면, MST가 완성된다!

### Example : 백준 1197 최소 스패닝 트리
{% highlight cpp %}
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int parent[10001]; // 그래프
long long answer = 0;
vector<pair<int, pair<int, int>>> graph;
void init_map(int size) {
	for (int i = 1; i <= size; i++)
		parent[i] = i;
}

int get_parent(int node) {
	if (parent[node] == node) return node;
	return parent[node] = get_parent(parent[node]);
}

bool checkCycle(int node_x, int node_y) {
	node_x = get_parent(node_x);
	node_y = get_parent(node_y);

	if (node_x == node_y)
		return true;

	return false;
}

void do_union(int node_x, int node_y) {
	node_x = get_parent(node_x);
	node_y = get_parent(node_y);

	if (node_x < node_y) parent[node_x] = node_y;
	else parent[node_y] = node_x;
}

int main() {
	int ver = 0, ed = 0;
	int node_x = 0, node_y = 0, weight = 0;

	cin >> ver >> ed;
	for (int i = 0; i < ed; i++) {
		cin >> node_x >> node_y >> weight;
		graph.push_back({ weight, { node_x, node_y } });
	}

	init_map(ver);
	sort(graph.begin(), graph.end()); // 가중치 오름차순 정렬

	// 이제부터 간선을 잇는다.
	for (int i = 0; i < ed; i++) {
		if (!checkCycle(graph[i].second.first, graph[i].second.second)) {
			do_union(graph[i].second.first, graph[i].second.second);
			answer += graph[i].first;
		}
	}

	cout << answer;

	return 0;
}
{% endhighlight %}
