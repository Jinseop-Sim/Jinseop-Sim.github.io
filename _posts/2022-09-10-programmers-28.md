---
layout: post
title: "프로그래머스 Level 3 - 입국 심사"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [입국 심사[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/43238)  

처음엔, 진짜로 동작을 구현을 해서 최소 시간을 구하려고 시도했다.  
하지만, 하다보니 이는 구현은 분명 할 수 있지만 시간이 너무 오래걸리는 방식이다.  
한마디로 __무식한__ 방식임을 금방 깨달을 수 있었다.  

그래서 도저히 생각이 나지 않아, 결국 다른 사람의 풀이를 참고했다.  

문제의 분류가 이분 탐색이어서 이걸 어디다 이용해야 하나 매우 고민을 했었는데,  
정답은 시간에 이분 탐색을 이용하는 것이었다.  

최소 시간(1초) 부터 최대 시간까지 구간을 두고 그 구간들을 이분 탐색하는 것이다.  
이 때, 최대 시간은 가장 느린 심사관이 모든 고객을 처리했을 때 걸리는 시간이다.  

### Binary Search
{% highlight cpp %}
#include <iostream>
#include <algorithm>
#include <vector>
using namespace std;

void binary_search(long long& answer, int n, long long st, long long end, vector<int> times) {
    while (st <= end) {
        long long mid = (st + end) / 2;
        // 이분탐색을 위해 최소, 최대 구간을 반으로 나눈다.
        long long client = 0;
        // 처리 가능한 고객의 수

        for (int i = 0; i < times.size(); i++)
            client += (mid / (long long)times[i]);
        // 이 부분이 핵심 코드!!
        // 현재 시간을 times로 나눈다? 해당 시간만에 몇 명을 처리할 수 있는가!

        if (client >= n) {
        // 해당 시간 안에 모든 심사관이 모든 고객을 처리한다면?
            end = mid - 1;
            answer = mid;
        // 정답을 수정하고, 최대 시간을 현재 시간으로 수정한다.

        }
        else
            st = mid + 1;
        // 해당 시간 안에 모든 심사관이 처리를 할 수 없다면
        // 최소 시간을 현재 시간으로 수정한다.
        // 물론, 정답이 될 수 없으므로 정답은 수정하지 않는다.
    }
}

long long solution(int n, vector<int> times) {
    long long answer = 0;
    sort(times.begin(), times.end());
    // 최대 시간을 미리 구하기 위해 정렬.

    long long st = 1;
    long long end = n * (long long)times.back();
    // 심사관들이 처리했을 때 가장 늦는 시간.

    binary_search(answer, n, st, end, times);

    return answer;
}
{% endhighlight %}