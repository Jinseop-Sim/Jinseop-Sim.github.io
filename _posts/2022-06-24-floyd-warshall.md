---
layout: post
title: "Shortest Path : Floyd Warshall"
categories: Algorithm
tags: [cpp]
author:
  - Jinseop Sim
---

## Floyid-Warshall
> 모든 정점에서 다른 정점으로의 최단 경로를 구하는 Algorithm이다.  

![image](https://user-images.githubusercontent.com/71700079/174823499-627fdd15-abe4-44ef-b712-3d83efc5f68a.png)  

- 노드의 갯수가 N개라고 했을 때, N개에서 도달할 수 있는 모든 정점에 대해서 거리를 계산하기 때문에, Dijkstra를 N번 한다고 보면 되겠다.
- DP를 기반으로 하기 때문에, 아래와 같은 점화식을 갖는다.  
![image](https://user-images.githubusercontent.com/71700079/174823615-e2e398c4-22be-4188-891e-2b8241c1ed4a.png)  

- STEP 1
  - 처음 갱신되는 Table은 아래와 같다.  
  ![image](https://user-images.githubusercontent.com/71700079/175305210-4b898d31-5e77-463e-a86c-fefd7a08f91c.png)  
- STEP 2
  - 이후 1번 Node를 거쳐가는 경우를 고려하여 Table을 갱신한다.  
  ![image](https://user-images.githubusercontent.com/71700079/175305333-dd61d61c-3a43-4536-bfca-9d3e617fe296.png)   
- STEP 3
  - 이후 2번 Node를 거쳐가는 경우를 고려하여 Table을 갱신한다.  
  ![image](https://user-images.githubusercontent.com/71700079/175305436-7689bae2-3512-4f7e-b88f-fca14bec3e1a.png)  

- 이후 단계들은 Table 갱신의 반복이다.
- 시간 복잡도는 노드 수가 N개일 때, 단계마다 O(N^2)의 연산을 반복하므로, O(N^3)이 된다.

### Example
{% highlight cpp %}
#include <iostream>
#include <vector>
using namespace std;
#define INF 1000000000

int nodes = 0, bus = 0;
int cities[101][101];

void make_input() {
    for (int i = 0; i < bus; i++) {
        int src, dest, d;
        cin >> src >> dest >> d;
        if(cities[src][dest] > d)
            cities[src][dest] = d;
    }
}

void init_map() { // Initialize with INFINITE
    for (int i = 1; i <= nodes; i++) {
        for (int j = 1; j <= nodes; j++) {
            if (i == j) cities[i][j] = 0;
            else cities[i][j] = INF;
        }
    }
}

void floyd_warshall() { // floyid warshall
    for (int sol = 1; sol <= nodes; sol++) {
        for (int i = 1; i <= nodes; i++) {
            for (int j = 1; j <= nodes; j++) {
                if (cities[i][sol] != INF && cities[sol][j] != INF) {
                    cities[i][j] = min(cities[i][j], cities[i][sol] + cities[sol][j]);
                }
            }
        }
    }
}

void print_answer() {
    for (int i = 1; i <= nodes; i++) {
        for (int j = 1; j <= nodes; j++) {
            if (cities[i][j] == INF) cout << 0 << " ";
            else cout << cities[i][j] << " ";
        }
        cout << '\n';
    }
}

int main() {
    cin >> nodes >> bus;

    init_map();
    make_input();
    floyd_warshall();
    print_answer();

    return 0;
}
{% endhighlight %}