---
layout: post
title: "프로그래머스 Level 2 - 배달"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [배달[LEVEL 2]](https://school.programmers.co.kr/learn/courses/30/lessons/12978) 

나는 보자마자 다익스트라 알고리즘으로 풀면 되겠구나 싶었다.  
내가 생각한 풀이 방식은 다음과 같다.  

- 1번 마을로 부터 모든 마을에 대한 배달 시간을 요구하고 있다.
  - One to Many의 방식이므로, 다익스트라가 적합하다.
- 다익스트라로 풀기 위해 주어진 입력을 인접 리스트로 구현한다.
  - 이 때, 양방향 그래프이므로 양 쪽 모두 추가해야 한다.
- 모두 구해진 거리 배열에서 K 이하인 마을의 수만 세주면 된다.

물론 다르게 푸는 방법도 많이 존재할 것이다.  

{% highlight cpp %}
#include <vector>
#include <queue>
using namespace std;
#define INF 100000000

priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> node_q;
vector<pair<int, int>> maps[51];
int dist[51];

void make_map(vector<vector<int>> road) {
    for (auto i : road) {
        maps[i[0]].push_back({ i[1], i[2] });
        maps[i[1]].push_back({ i[0], i[2] });
    }
}
// 양방향 그래프 인접리스트 구현

void init_dist(int n) {
    for (int i = 1; i < n + 1; i++)
        dist[i] = INF;
}
// 거리 배열 초기화

void dijkstra(int start) {
    dist[start] = 0;
    node_q.push({ 0, start }); 
    // first 기준으로 정렬하기 때문에 이 순서로 저장.

    while (!node_q.empty()) {
        int cost = node_q.top().first;
        int curr = node_q.top().second;
        node_q.pop();

        if (dist[curr] < cost) continue;

        for (int i = 0; i < maps[curr].size(); i++) {
            int next = maps[curr][i].first;
            int next_cost = cost + maps[curr][i].second;
            if (next_cost < dist[next]) {
                dist[next] = next_cost;
                node_q.push({ next_cost, next});
            }
        }
    }
}

int solution(int N, vector<vector<int>> road, int K) {
    int answer = 0;

    make_map(road);
    init_dist(N);
    dijkstra(1);

    for (int i = 1; i < N + 1; i++)
        if (dist[i] <= K) answer++;

    return answer;
}
{% endhighlight %}