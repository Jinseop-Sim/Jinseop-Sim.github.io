---
layout: post
title: "Search : Parametric Search"
categories: Algorithm
tags: [cpp]
author:
  - Jinseop Sim
math: true
toc: true
---
우리는 과연 이분 탐색을 단순히 정렬이나 수 찾기에만 쓸 수 있을까?  
이분 탐색을 이용하는 탐색법인 __Parametric Search__ 에 대해 알아보자.  

### Parametric Search?
Parametric Search는 최적화 문제를 이분 탐색으로 풀어나가는 방법이다.  

> 최적화 문제를 결정 문제(Decision Problem)로 바꾸어 풀 것.  

최적화 문제를 이분 탐색을 통해 범위를 좁혀가며 답을 찾아내는 것이다.  

### Example : 프로그래머스 - 금과 은 운반하기
[금과 은 운반하기[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/86053)  

{% highlight cpp %}
#include <string>
#include <vector>
using namespace std;

long long solution(int a, int b, vector<int> g, vector<int> s, vector<int> w, vector<int> t) {
    long long answer = 10e5 * 4 * 10e9;
    // 기본적으로 답은 범위의 최대값으로 설정한다.

    long long start = 0;
    long long end = 10e5 * 4 * 10e9;
    int truck_num = s.size();

    while (start <= end) {
        long long mid = (start + end) / 2;

        long long gold = 0; // 총 운반해야할 금, 은
        long long silver = 0;
        long long carry_sum = 0; // 총 운반해야할 금속

        for (int i = 0; i < truck_num; i++) {
            long long curr_gold = (long long)g[i]; // 현재 도시의 금, 은 한도
            long long curr_silver = (long long)s[i];
            long long curr_weight = (long long)w[i]; // 현재 수레의 최대 무게
            long long curr_time = (long long)t[i]; // 현재 수레의 소요 시간

            long long how_many_move = mid / (curr_time * 2);
            // 최대 시간 안에 현재 수레는 몇 번 왔다갔다 하는가?

            if (mid % (curr_time * 2) >= t[i]) how_many_move++;
            // 왔다갔다 했을 때, 시간이 남는 경우는 편도로 한 번더 가야하는 경우.

            gold += (curr_gold < how_many_move * curr_weight) ? curr_gold : how_many_move * curr_weight;
            // 현재 도시의 금 한도에 비해 운반가능한 전체 무게가 더 크면?
            // 현재 도시의 금을 운반해야할 금에 더한다.
            // 아니면, 운반 가능한 전체 무게만 더한다.

            silver += (curr_silver < how_many_move * curr_weight) ? curr_silver : how_many_move * curr_weight;
            // 위와 동일.

            carry_sum += (curr_gold + curr_silver < how_many_move * curr_weight) ? curr_gold + curr_silver : how_many_move * curr_weight;
            // 현재 도시의 금, 은 한도 총합에 비해 전체 운반 가능 무게가 더 크면?
            // 현재 도시의 금, 은 한도 총합을 더해버린다.
            // 아니면, 운반 가능한 무게 만큼만 더한다.
        }

        if (gold >= a && silver >= b && carry_sum >= a + b) {
            // 모든 금, 은이 운반 가능하다면
            end = mid - 1; // 범위를 작은쪽으로 줄인다.
            answer = min(mid, answer); // answer을 더 작은쪽으로 갱신.
        }
        else
            start = mid + 1;
            // 운반이 불가능한 범위라면 범위를 큰 쪽으로 줄인다.
    }

    return answer;
}
{% endhighlight %}