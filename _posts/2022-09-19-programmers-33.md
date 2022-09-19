---
layout: post
title: "프로그래머스 Level 3 - 디스크 컨트롤러"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [디스크 컨트롤러[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/12907)  

작업의 우선순위를 결정하는 문제이므로, 우선순위 큐를 써야겠다고 생각했다.  
내가 고려해야겠다고 생각한 점은 다음과 같다.  

- 요청이 들어오는 순서는 정해져 있다.
- 하지만 요청이 먼저 들어왔더라도 작업 시간이 짧으면 먼저 처리한다.
  - 위 두 가지 요소를 처리하기 위해 2가지 방안을 사용할 수 있을 것이다.  
    - 작업 시간이 짧은 것을 뽑는 것은 우선순위 큐(Heap)를 사용한다.
    - 요청이 먼저 들어온 순서대로 큐에 넣어야 하므로, 원래 배열을 정렬한다.
- 선 작업이 끝나기 전 요청은 모두 대기를 시켜놓아야 한다.
  - 이는 현재 작업의 완료 시간보다 도착시간이 빠른 일은 모두 큐로 집어넣도록 한다.

일종의 Sweep Algorithm이라고 생각한다.  
최종 구현은 아래와 같다.  

{% highlight cpp %}
#include <vector>
#include <queue>
#include <algorithm>
using namespace std;

struct cmp {
    bool operator()(vector<int>& a, vector<int>& b) {
        return a[1] > b[1];
    }
    // 우선순위 큐를 오름차순으로 뽑아낼 정렬 구조체
};

priority_queue<vector<int>, vector<vector<int>>, cmp> job_classifier;
vector<int> task_time;
void reduce_avg_time(vector<vector<int>> jobs) {
    int timer = 0, task_cnt = 0;

    while (task_cnt < jobs.size() || !job_classifier.empty()){
    // 작업의 수가 전체 일 갯수보다 작거나, PQ가 비지 않았으면 계속 작업한다.

        if (task_cnt < jobs.size() && jobs[task_cnt][0] <= timer) {
        // 전체 작업이 안끝났고,
        // 가장 빠른 요청의 작업이 현재 시간보다 작거나 같으면?
        // 우선순위 큐에 삽입!

            job_classifier.push(jobs[task_cnt]);
            task_cnt++; continue;
            // 처리한 작업의 수를 늘려주어야 한다.
            // continue를 하는 이유는?
            // Timer보다 도착시간이 빠른 일은 모두 PQ에 집어 넣기 위함!
        }

        if (!job_classifier.empty()) {
            // 작업이 남았으면?

            timer += job_classifier.top()[1];
            // 요청시간이 가장 짧은 작업시간을 Timer에 더해준다.

            task_time.push_back(timer - job_classifier.top()[0]);
            // 평균 계산을 위한 처리시간을 Vector에 저장.

            job_classifier.pop();
            // 해당 작업은 완료!
        }

        else timer = jobs[task_cnt][0];
        // 작업 큐는 비었는데 할 일이 남았으면?
        // 다음 일이 시작되는 시간으로 Timer을 올린다.
    }
}

int calc_avg_time() {
    int sum = 0;

    for (int i : task_time)
        sum += i;

    return (sum / task_time.size());
}

int solution(vector<vector<int>> jobs) {
    int answer = 0;

    sort(jobs.begin(), jobs.end());
    // 빠른 작업 요청 순서로 정렬이 필요하다!

    reduce_avg_time(jobs);
    answer = calc_avg_time();

    return answer;
}
{% endhighlight %}