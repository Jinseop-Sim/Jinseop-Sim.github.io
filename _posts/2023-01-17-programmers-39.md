---
layout: post
title: "프로그래머스 Level 3 - 부대복귀"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [부대 복귀[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/132266)  

### Floyd Warshall
당연히 시간 초과가 발생할 거라 생각하면서, 혹시 몰라 시도해보았다.  
N의 최대값이 100,000인데 O(N^3)의 시간 초과는 당연한 결과였다.  
분명 정답은 나오겠지만, 시간적 효율성이 너무 떨어진다!  

{% highlight cpp %}
#include <string>
#include <vector>
#include <iostream>
using namespace std;
#define INF 9999999;

void init_map(int size, vector<vector<int>> roads, vector<vector<int>>& fw_map) {
    for (int i = 1; i <= size; i++)
        for (int j = 1; j <= size; j++) {
            if (i == j)
                continue;
            else
                fw_map[i][j] = INF;
        }

    for (auto vec : roads) {
        fw_map[vec[0]][vec[1]] = 1;
        fw_map[vec[1]][vec[0]] = 1;
    }
}

void fill_map(int size, vector<vector<int>>& fw_map) {
    for (int mid = 1; mid <= size; mid++) {
        for (int i = 1; i <= size; i++) {
            for (int j = 1; j <= size; j++) {
                fw_map[i][j] = min(fw_map[i][j], fw_map[i][mid] + fw_map[mid][j]);
            }
        }
    }
}

void fill_answer(vector<vector<int>> fw_map, vector<int> sources, int destination, vector<int>& answer) {
    for (auto i : sources) {
        int input_val = fw_map[i][destination];
        if (input_val == 9999999)
            answer.push_back(-1);
        else
            answer.push_back(input_val);
    }
}

vector<int> solution(int n, vector<vector<int>> roads, vector<int> sources, int destination) {
    vector<int> answer;
    vector<vector<int>> fw_map(n+1, vector<int>(n+1, 0));

    init_map(n, roads, fw_map);
    fill_map(n, fw_map);
    fill_answer(fw_map, sources, destination, answer);

    return answer;
}
{% endhighlight %}

### Dijkstra
