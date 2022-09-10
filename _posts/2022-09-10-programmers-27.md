---
layout: post
title: "프로그래머스 Level 3 - 가장 먼 노드"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [가장 먼 노드[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/49189)  

1번 노드에서 가장 먼 노드들의 수를 구하는 문제였다.  
특정 정점에서 다른 노드들까지의 거리를 계산하는 문제? 다익스트라 문제다.  

### Dijkstra
{% highlight cpp %}
#include <algorithm>
#include <vector>
#include <queue>
using namespace std;
#define INF 9999999
#define START 1

int dist[20001];
vector<pair<int, int>> graph[20001];
priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> dijk_q;
void init_graph(int n, vector<vector<int>> vertex) {
    for (auto ver : vertex) {
        graph[ver[0]].push_back({ ver[1], 1 });
        graph[ver[1]].push_back({ ver[0], 1 });
    }
    // 주어진 node를 인접 리스트로 표현.

    for (int i = 1; i <= n; i++)
        dist[i] = INF;
    // 다익스트라를 위한 초기화 작업

    dijk_q.push({START, 0});
    dist[START] = 0;
    // 1번 node는 거리가 0
}

void dijkstra_with_pq() {
    while (!dijk_q.empty()) {
        int curr_node = dijk_q.top().first;
        int cost = dijk_q.top().second;
        dijk_q.pop();
        // 미리 넣어놓았던 시작노드 부터 정보 추출

        if (dist[curr_node] < cost) continue;
        // 만약 새 비용보다 현재 거리가 짧다면 탐색할 필요 X!

        for (int i = 0; i < graph[curr_node].size(); i++) {
            int next_node = graph[curr_node][i].first;
            int next_cost = cost + graph[curr_node][i].second;
            // 해당 노드에 연결된 다른 노드와 해당 노드까지의 거리 계산
            // next_cost는 1번노드 -> 현재 노드 -> 다음 노드까지의 거리 합

            if (next_cost < dist[next_node]) {
                // 거리 합이 기존 dist보다 작으면 교체해주어야 한다.
                dist[next_node] = next_cost;
                dijk_q.push({ next_node, next_cost });
            }
        }
    }
}

int count_max_node(int n) {
    int max_node = *max_element(dist, dist+n);
    int cnt = 0;

    for (int i = 1; i <= n; i++)
        if (dist[i] == max_node) cnt++;

    return cnt;
}

int solution(int n, vector<vector<int>> vertex) {
    int answer = 0;

    init_graph(n, vertex);
    dijkstra_with_pq();
    answer = count_max_node(n);

    return answer;
}
{% endhighlight %}