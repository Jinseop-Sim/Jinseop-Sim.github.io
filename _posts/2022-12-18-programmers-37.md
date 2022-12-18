---
layout: post
title: "프로그래머스 Level 2 - 디펜스 게임"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [디펜스 게임[LEVEL 2]](https://school.programmers.co.kr/learn/courses/30/lessons/142085)  

Min heap, 즉 우선 순위 큐로 풀면 간단하게 풀 수 있는 문제였다.  
다만 자료구조를 생각해도 그 해법을 생각하기 까지가 시간이 오래 걸렸다.  
생각의 흐름은 다음과 같다.  

- ```무적권```은 최대한 적군이 많은 라운드에 사용함이 적절하다.
  - 하지만, 정렬을 이용하면 라운드가 뒤바뀌기 때문에 그 방법은 옳지 않다.
  - 그럼 반대로 ```무적권```을 사용하지 않아도 되는 라운드를 골라내자!
- 최소 힙을 이용해 적의 수가 작은 Round는 병사를 소모해버리도록 하자.
  - 단, 최소 힙의 크기가 사용할 수 있는 무적권의 수를 넘었을 때부터 뽑아낸다.

### Priority Queue
{% highlight cpp %}
#include <vector>
#include <iostream>
#include <queue>
using namespace std;

priority_queue<int, vector<int>, greater<int>> classifer;
int solution(int n, int k, vector<int> enemy) {
    int cost = 0;
    int enemies = enemy.size();

    for (int i = 0; i < enemy.size(); i++) {
        classifer.push(enemy[i]);

        if (classifer.size() > k) { // 무적권을 사용할 수 있는 Round
            cost += classifer.top();
            classifer.pop();
        }

        if (cost > n)
            return i;
    }

    return enemies;
}
{% endhighlight %}