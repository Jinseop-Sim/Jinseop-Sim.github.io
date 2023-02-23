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

### 도식화
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
#include <vector>
#include <iostream>
using namespace std;

int nodes[1000001];
void init_nodes(int size) {
    for (int i = 1; i <= size; i++)
        nodes[i] = i;
}

int get_root(int node_x) { 
    // 해당 node의 Root를 찾는다.
    if (nodes[node_x] == node_x) return node_x;
    return nodes[node_x] = get_parent(nodes[node_x]);
}

void do_union(int node_x, int node_y) {
    // Root끼리 연결을 해주는 함수
    node_x = get_parent(node_x);
    node_y = get_parent(node_y);

    if (node_x > node_y) nodes[node_x] = node_y;
    else nodes[node_y] = node_x;
}

void find(int node_x, int node_y) {
    // Root가 같으면? 연결된 Tree(같은 집합)!
    node_x = get_parent(node_x);
    node_y = get_parent(node_y);

    if (node_x == node_y) cout << "YES\n";
    else cout << "NO\n";
}

int main() {
    int n = 0, m = 0;
    int cmd = 0, node_x = 0, node_y = 0;
    
    cin >> n >> m;
    init_nodes(n);

    for (int i = 0; i < m; i++) {
        cin >> cmd >> node_x >> node_y;

        if (cmd == 0)
            do_union(node_x, node_y);
        else
            find(node_x, node_y);
    }

    return 0;
}
{% endhighlight %}

> 참고 자료 출처 : [브랜든의 블로그](https://brenden.tistory.com/33)

### MST
Tree의 종류 중에 MST(Minimum Spanning Tree)라는 Tree가 있다.  
해당 Tree는 위에서 학습한 Union Find로 구현이 가능한 Tree이다.  

먼저 Spanning Tree부터 알아보자.  
- Spanning Tree : Cycle 없이 모든 Node를 이은 Tree.
  - 해당 Tree는 당연히 Link(Edge)의 수가 ```Node(Vertex) - 1```이 된다.

그럼 Minimum Spanning Tree란 뭘까?  
바로 Spanning Tree 중 가중치가 가장 작은 Tree를 의미한다.  
즉, 각 Edge에 가중치가 존재한다고 가정을 하고 구현하는 Tree이다.  

아래부터 MST를 구할 수 있는 알고리즘을 소개하도록 하겠다.  

### Kruskal Algorithm
MST를 구할 수 있는 첫번째 알고리즘, Kruskal 알고리즘이다.  
위에서 언급했듯이 ```Union Find```를 이용해 MST를 구할 수 있다고 했는데,  
바로 Kruskal Algorithm에 ```Union Find```가 사용된다.  

아래와 같은 순서로 진행된다.  
1. Graph의 모든 간선 정보를 빼내어 오름차순 정렬한다.
2. 비용이 가장 최소인 간선부터 두 노드를 연결해준다.
  - 해당 동작에 Union Find의 Union 동작이 들어간다.
3. 계속 뒤로가며 Union을 반복한다.
  - 이 때, ```Union Find```의 특징적인 동작이 보인다.
  - 반복시에 Cycle이 발생하면 MST에 포함시키지 않는다.
    - 이는 ```Union Find```의 ```find()```를 이용하는 방식이다.
    - 두 노드의 부모가 같으면, Cycle의 발생을 의미한다!
4. 모든 배열을 다 순회하고 나면, MST가 완성된다!