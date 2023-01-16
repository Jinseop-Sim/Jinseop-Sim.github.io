---
layout: post
title: "프로그래머스 Level 3 - 섬 연결하기"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [디펜스 게임[LEVEL 2]](https://school.programmers.co.kr/learn/courses/30/lessons/42861)  

오랜만에 코딩 테스트 공부를 했다.  
오랜만에 하자마자 만난 문제가 처음 만나보는 알고리즘 문제였다.  

### Floyd-Warshall
처음에는 Floyd-Warshall 알고리즘으로 풀려고 시도했다.  
왜냐하면, Floyd-Warshall은 모든 정점에서 시작하는 최단경로를 구하기 때문이다.  

하지만, Floyd-Warshall을 사용하는 경우에는 다음과 같은 문제가 발생한다.  
- Floyd-Warshall은 정점과 정점간의 최단 경로 밖에 구하지 못한다.
  - 즉, 모든 정점을 지나왔는지 알 방법이 없다는 말이다.
- 그렇다면 모든 정점에서 시작하는 DFS와 결합을 한다면?
  - 시간 복잡도 상 좋지 않은 Code가 될 것이다.
  - 이미 Floyd-Warshall 부터가 O(N^3)인 상태이다.

위와 같은 이유로 다른 알고리즘을 생각해보다 결국 검색을 했다.  

### Kruskal
__Kruskal Algorithm__ 은 MST를 구하는 알고리즘이다.  
여기서 MST란 Minimum Spannig Tree로, 즉 __최소 신장 트리__ 를 의미한다.  

- Spanning Tree란?
  - 순환 없이 모든 노드가 연결된 그래프를 말한다.
  - 간선의 수가 최소 간선으로, ```정점의 수 - 1```이 된다.
  - 사실상, 정점을 Root로 모든 그룹 멤버들을 자식으로 갖는 Tree 구조이다.
- 그럼 MST는?
  - Spanning Tree는 여러개가 될 수 있는데, 그 중 가중치의 합이 가장 작은 Tree이다.

{% highlight cpp %}
#include <string>
#include <vector>
#include <algorithm>
#include <iostream>
using namespace std;

int kr_map[101];

void init_map(int size) {
    for (int i = 0; i < size; i++)
        kr_map[i] = i; // 아직 부모가 없으니, 자기자신으로 초기화 한다.
}

bool cmp(vector<int> a, vector<int> b) {
    return a[2] < b[2]; // 주어진 간선들을 오름차순 정렬한다.
}

int get_parent(int curr) {
    if (kr_map[curr] == curr)
        return curr;

    return kr_map[curr] = get_parent(kr_map[curr]);
}

void kruskal(int& answer, vector<vector<int>> costs) {
    for (int i = 0; i < costs.size(); i++) {
        int start = get_parent(costs[i][0]);
        int end = get_parent(costs[i][1]);
        int cost = costs[i][2];

        if (start != end) {
            answer += cost;
            kr_map[end] = start;
        }
    }
}

int solution(int n, vector<vector<int>> costs) {
    int answer = 0;
    
    init_map(n);
    sort(costs.begin(), costs.end(), cmp);
    kruskal(answer, costs);

    return answer;
}
{% endhighlight %}