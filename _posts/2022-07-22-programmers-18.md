---
layout: post
title: "프로그래머스 Level 2 - 2개 이하로 다른 비트 "
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---

- [합승 택시 요금[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/72413)

KAKAO 2021 BLIND RECRUITMENT의 문제였다.  
Graph 문제길래, 아무 생각없이 탐색 문제로 접근하려다가 낭패를 봤다.  

도저히 생각이 안나서 풀이를 참고해서 이해했다.  
풀이 방식은 다음과 같다.  
1. 시작 지점에서 특정 지점까지 합승을 한다.
2. 이후 갈라진 다음에도 최소 비용이 유지되어야 한다.
3. 즉, 우리는 모든 정점 간의 최소 비용을 알고 있어야 한다!

위 생각에 따라, 풀이는 Floyd Warshall로 진행한다.  

### 풀이 : Floyd Warshall

{% highlight cpp %}
#include <string>
#include <vector>
using namespace std;
#define INF 10000000

int dist[201][201];

void init_dist(int n) {
    // Floyd Warshall 초기화
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= n; j++)
            dist[i][j] = INF;

    for (int i = 1; i <= n; i++) dist[i][i] = 0;
}

void make_graph(vector<vector<int>> fares) {
    for (auto fare : fares) {
        dist[fare[0]][fare[1]] = fare[2];
        dist[fare[1]][fare[0]] = fare[2];
    }
}

void floyd_warshall(int n) {
    for (int mid = 1; mid <= n; mid++) {
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= n; j++) {
                dist[i][j] = min(dist[i][mid] + dist[mid][j], dist[i][j]);
            }
        }
    }
}

int solution(int n, int s, int a, int b, vector<vector<int>> fares) {
    int answer = INF;

    init_dist(n);
    make_graph(fares);
    floyd_warshall(n);

    for (int i = 1; i <= n; i++)
        answer = min(answer, dist[s][i] + dist[i][a] + dist[i][b]);
        // 이 말은 시작 ==> 특정 지점 + 특정 지점 ==> A + 특정 지점 ==> B 가 된다.

    return answer;
}
{% endhighlight %}