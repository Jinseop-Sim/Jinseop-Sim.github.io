---
layout: post
title: "���α׷��ӽ� Level 3 - �� �����ϱ�"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [���潺 ����[LEVEL 2]](https://school.programmers.co.kr/learn/courses/30/lessons/42861)  

�������� �ڵ� �׽�Ʈ ���θ� �ߴ�.  
�������� ���ڸ��� ���� ������ ó�� �������� �˰��� ��������.  

### Floyd-Warshall
ó������ Floyd-Warshall �˰������� Ǯ���� �õ��ߴ�.  
�ֳ��ϸ�, Floyd-Warshall�� ��� �������� �����ϴ� �ִܰ�θ� ���ϱ� �����̴�.  

������, Floyd-Warshall�� ����ϴ� ��쿡�� ������ ���� ������ �߻��Ѵ�.  
- Floyd-Warshall�� ������ �������� �ִ� ��� �ۿ� ������ ���Ѵ�.
  - ��, ��� ������ �����Դ��� �� ����� ���ٴ� ���̴�.
- �׷��ٸ� ��� �������� �����ϴ� DFS�� ������ �Ѵٸ�?
  - �ð� ���⵵ �� ���� ���� Code�� �� ���̴�.
  - �̹� Floyd-Warshall ���Ͱ� O(N^3)�� �����̴�.

���� ���� ������ �ٸ� �˰����� �����غ��� �ᱹ �˻��� �ߴ�.  

### Kruskal
__Kruskal Algorithm__ �� MST�� ���ϴ� �˰����̴�.  
���⼭ MST�� Minimum Spannig Tree��, �� __�ּ� ���� Ʈ��__ �� �ǹ��Ѵ�.  

- Spanning Tree��?
  - ��ȯ ���� ��� ��尡 ����� �׷����� ���Ѵ�.
  - ������ ���� �ּ� ��������, ```������ �� - 1```�� �ȴ�.
  - ��ǻ�, ������ Root�� ��� �׷� ������� �ڽ����� ���� Tree �����̴�.
- �׷� MST��?
  - Spanning Tree�� �������� �� �� �ִµ�, �� �� ����ġ�� ���� ���� ���� Tree�̴�.

{% highlight cpp %}
#include <string>
#include <vector>
#include <algorithm>
#include <iostream>
using namespace std;

int kr_map[101];

void init_map(int size) {
    for (int i = 0; i < size; i++)
        kr_map[i] = i; // ���� �θ� ������, �ڱ��ڽ����� �ʱ�ȭ �Ѵ�.
}

bool cmp(vector<int> a, vector<int> b) {
    return a[2] < b[2]; // �־��� �������� �������� �����Ѵ�.
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