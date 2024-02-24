---
layout: post
title: "프로그래머스 Level 3 - 선입 선출 스케줄링"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [선입 선출 스케줄링[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/12920)

이번 문제는 프로그래머스의 레벨 3 선입 선출 스케줄링이다.  
문제가 굉장히 짧지만, 차근차근 읽어보도록 하자.  

먼저 최대 ```10000```개의 코어와 함께 처리시간이 배열로 주어진다.  
각 코어의 처리시간은 최대 ```10000```시간 까지 주어진다.  
또한 일의 개수가 최대 ```50000```개 주어진다.  
각 코어가 일을 모두 끝낼 수 있는 최적의 경우로 일을 했을 때,  
가장 마지막 작업을 처리하게 되는 코어의 번호를 출력하라!  

어떻게 문제를 풀어야 할까?  

### Greedy?
가장 먼저 든 생각은 그리디하게 풀 수 있지 않을까? 였다.  
코어를 처리 시간 기준 오름차순으로 정렬했다고 생각해보자.  
앞에서 부터 작업을 분배하여 차례차례 구현한 다음,  
마지막 작업을 수행하는 코어의 인덱스를 직접 찾는 것이다.  

즉, __그리디하게 처리 시간이 짧은 코어 부터 선택__ 하는 것이다.  
직접 찾으려면 시간 초과가 발생하지는 않을까?  
효율적인 로직을 위해 ```Queue``` 자료 구조를 한 번 이용해보자.  

우선순위 큐에 현재 진행 중인 작업을 집어 넣고, 현재 시간과 진입 시간을 비교한다.  
```현재 시간 + 처리 시간```이 이후 현재 시간과 같아지는 순간 큐에서 뺀다.  
큐에서 빠지게 되면, 동시에 새로운 일을 수행할 수 있다는 것!  
곧바로 해당 코어에게 새로운 일을 배정하고, 큐에 다시 집어 넣는다.  

위와 같이 반복적으로 처리하면, 마지막에 큐에 남는 코어가 답이 될 것이다.  
아래와 같이 구현해보았다.  
{% highlight cpp %}
struct cmp {
	bool operator()(piii a, piii b) {
		if (a.first.first == b.first.first)
			return a.second > b.second;
		
		return a.first.first > b.first.first;
	}
};

unordered_map<int, int> core_index_map;
int solution(int n, vector<int> cores) {
	int answer = 0;
	int size = cores.size(), iter = 0;

	for (int i = 0; i < size; i++)
		core_index_map[cores[i]] = i + 1;
	sort(cores.begin(), cores.end());

	priority_queue<piii, vector<piii>, cmp> core_q;
	for (int i = 0; i < cores.size(); i++) {
		core_q.push({ { cores[i], cores[i] }, i });
		iter = i;
	}

	int timer = 1;
	bool finished = false;
	while (true) {
		while (!core_q.empty() && (core_q.top().first.first == timer)) {
			iter++;
			core_q.push({ { timer + core_q.top().first.second, core_q.top().first.second}, iter });
			n--;

			if (n == 0) {
				finished = true;
				answer = core_q.top().first.second;
				break;
			}

			core_q.pop();
		}

		if (finished)
			break;

		timer++;
	}

	answer = core_index_map[answer];
	return answer;
}
{% endhighlight %}

### 이분 탐색
안타깝게도 위의 로직은 효율성 케이스에서 시간초과가 발생했다.  
