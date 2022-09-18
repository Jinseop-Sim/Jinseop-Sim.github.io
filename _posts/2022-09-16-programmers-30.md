---
layout: post
title: "프로그래머스 Level 3 - 모두 0으로 만들기"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [모두 0으로 만들기[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/76503)  

처음엔 Edges 배열을 Backtracking을 이용해 순열을 구현했다.
순열을 통해 방문 순서를 바꿔가며 가중치를 가감하며 모든 경우에서 걸러내는 방식이었다.  
A 배열이 모두 0이 되는 경우 중, 가감 횟수가 가장 작은 경우를 뽑아 내려고 했다.  

하지만 언제나 그랬듯.. 완전 탐색은 시간 초과를 초래했다..  

### 1차 시도 : Backtracking
{% highlight cpp %}
#include <iostream>
#include <vector>
using namespace std;
#define BIGINT 9999999

long long res_cnt = 0, min_v = BIGINT;
bool validate(vector<int> a) {
    int sum = 0;

    for (int i : a)
        sum += i;

    if (sum == 0) return true;
    else return false;
}

bool validate_zero(vector<int> a) {
    for (int i : a)
        if (i != 0) return false;

    return true;
}

void make_all_zero(int depth, vector<int>& a, vector<vector<int>> edges, vector<bool>& visit) {
    if (depth == edges.size()) {
        if (validate_zero(a))
            if (min_v > res_cnt)
                min_v = res_cnt;
        return;
    }
    
    for (int i = 0; i < edges.size(); i++) {
        if (!visit[i]) {
            visit[i] = true;
            int front_node = a[edges[i][0]];
            int back_node = a[edges[i][1]];

            a[edges[i][1]] += front_node;
            a[edges[i][0]] -= front_node;
            res_cnt += abs(front_node);

            make_all_zero(depth + 1, a, edges, visit);

            visit[i] = false;
            a[edges[i][1]] -= front_node;
            a[edges[i][0]] += front_node;
            res_cnt -= abs(front_node);
        }
    }
}

long long solution(vector<int> a, vector<vector<int>> edges) {
    long long answer = -1;
    vector<bool> visit(edges.size(), false);

    if (validate(a)) {
        make_all_zero(0, a, edges, visit);
        answer = min_v;
    }

    return answer;
}
{% endhighlight %}

### 2차 시도 : Tree의 구현
시간 초과를 없애기 위해 직접 Tree를 구현한 뒤 순회해보기로 했다.  
순회는 하였으나, 합을 쌓아나가는 과정에서 계속 시간초과가 발생해서  
결국 검색을 통해 다른 사람의 풀이를 참고했다.

{% highlight cpp %}
#include <iostream>
#include <vector>
using namespace std;

long long res_count = 0;
bool validate(vector<int> a) {
    // 모든 Node 가중치의 합이 0이 아니면
    // 애초에 DFS를 진행할 이유가 없음!
    long long sum = 0;

    for (int i : a)
        sum += i;

    if (sum == 0) return true;
    else return false;
}

void init_weight(vector<long long>& weight, vector<int> a) {
    for (int i = 0; i < a.size(); i++)
        weight[i] = a[i];
    // a의 한계치가 1,000,000이다.
    // 즉, 합이 매우 커질 수 있으므로 long long으로 변환한다.
}

void make_tree(vector<vector<int>>& adj_list, vector<vector<int>> edges, vector<int> a) {
    for (auto edge : edges) {
        adj_list[edge[0]].push_back(edge[1]);
        adj_list[edge[1]].push_back(edge[0]);
    }
    // Tree를 인접 리스트로 구현한다.
}

void make_all_zero(vector<vector<int>>& adj_list, vector<long long>& weight, int curr, int parent) {
    // 여기서 부턴 DFS Code
    for (int i = 0; i < adj_list[curr].size(); i++)
        if (adj_list[curr][i] != parent)
            make_all_zero(adj_list, weight, adj_list[curr][i], curr);
    
    // 더 이상 탐방할 Node가 없다면 아래의 행동을 한다.

    weight[parent] += weight[curr];
    // Leaf node로 부터 Parent node에 점점 값을 쌓아간다.
    // 마지막에 Root node에 쌓인 값만 0이면 됨!

    res_count += abs(weight[curr]);
    // 부모 노드에 쌓으면서 Count도 동시에 증가.
}

long long solution(vector<int> a, vector<vector<int>> edges) {
    long long answer = -1;
    vector<vector<int>> adj_list(a.size());
    vector<long long> weight(a.size());

    if (validate(a)) {
        init_weight(weight, a);
        make_tree(adj_list, edges, a);
        make_all_zero(adj_list, weight, 0, 0);
        answer = res_count;
    }
    
    return answer;
}
{% endhighlight %}