---
layout: post
title: "프로그래머스 Level 3 - 상담원 인원"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [상담원 인원[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/214288)

이번 문제는 프로그래머스의 레벨3 문제 상담원 인원이다.  
문제를 한 번 차근차근 읽어보자.  

상담을 원하는 여러명의 사람들에 대한 정보가 주어진다.  
최대 300명이 주어질 수 있으며, 시간에 대한 정보가 주어진다.  
시작 시간은 1000분까지, 소요 시간은 100분까지 가능하다.  
또한 최대 5 유형의 상담원이 분배 가능하며 최대 20명의 상담원이 있다.  

이 때 유형별 상담원을 잘 분배하여 기다리는 시간을 최소화 하자!  

### 알고리즘 파악
문제만 읽어봐도 아이디어를 잘 짜야할 것 같다.  
먼저 우리가 제어할 수 있는 요소들을 생각해보자.  

우리는 상담원의 유형을 제어할 수 있다.  
적어도 1명 이상의 상담원을 각 유형에 배치해야 한다.  
이외에는 제어할 수 있는 요소가 없다.  

Greedy 하게 풀 수 있을까 생각해보았는데, 안될 것 같다.  
상담시간이 긴 쪽으로 상담원을 몰아주거나 하는 풀이를 생각했는데,  
예외 사항이 꽤 많은 것 같다.  

그 다음으로 생각나는 것은 완전 탐색이다.  
상담원을 완전 탐색을 통해서 배분한 뒤, 시간 계산을 하는 것이다.  
백트래킹을 통해 상담원을 배분하는 모든 경우를 생각해보고,  
각 경우마다 걸리는 대기 시간을 계산하여 최소를 도출하는 것이다.  

시간 초과가 발생하지는 않을까?  
우선 시간을 계산하는 로직은 자료구조를 잘 이용해야 할 것 같다.  
우선순위 큐를 이용해서 계산하면, ```O(N)```만에 가능할 것 같다.  

그럼 백트래킹에 대한 시간 복잡도는 어떻게 될까?  
최대 ```20명```의 상담원을 ```5 유형```에 맞추는 경우의 수이다.  
사실 이는 ```P(20, 5)```, 즉 자연수의 분할 문제를 계산해야 하는데,  
인터넷에 검색을 한 결과 ```84```라는 결과를 볼 수 있었다.  
하지만 이 문제에선 유형이 정해져있어 순서가 다르면 다른 계산식이 되므로,  
더 많은 경우의 수가 나올텐데 시간 초과에 걸릴 정도는 아닌 것 같다.  

먼저 한 번 구현을 해보자.

### 백트래킹
먼저 상담원을 분배하는 모든 경우를 아래와 같이 구현했다.  
{% highlight cpp %}
void select_counselor(int member, vector<int> &counselors) {
	if (member == 0) {
    // calculate_waiting_time();
		return;
	}

	for (int i = 0; i < counselors.size(); i++) {
		counselors[i]++;
		select_counselor(member - 1, counselors);
		counselors[i]--;
	}
}
{% endhighlight %}

출력 결과, 모든 경우의 수가 잘 출력됨을 확인했다.  
여기까지는 굉장히 단순한 백트래킹 구현이었다.  
이제 효율적으로 대기 시간을 계산하는 것이 관건이다.  

나는 ```우선순위 큐```를 사용해서 대기 시간을 계산하려고 한다.  
상담은 당연히 먼저 시작한 사람을 우선으로 진행된다.  
그럼 앞사람이 끝나야 다음 사람이 상담을 시작할 수 있는 것이다.  

유형 별로 우선순위 큐를 만들어 상담실이라고 가정하도록 하자.  
그럼 현재 상담 신청을 한 사람의 시작 시간을 기준으로,  
현재 상담 가능한 상담원이 없는 경우에 대기 시간을 더해주면 될 것이다.  
최종적으로 아래와 같이 구현해보았다.  
{% highlight cpp %}
int calculate_waiting_time(vector<int> counselors, vector<vector<int>> &reqs) {
	int waiting_time = 0;
	vector<priority_queue<int, vector<int>, greater<int>>> counsel_q(counselors.size());
	// 상담 종료 시간, 상담원 유형

	for (int i = 0; i < reqs.size(); i++) {
		int curr_category = reqs[i][2];

		while (!counsel_q[curr_category - 1].empty() && counsel_q[curr_category - 1].top() <= reqs[i][0]) {
			counsel_q[curr_category - 1].pop();
			counselors[curr_category - 1]++;
		}

		if (counselors[curr_category - 1] == 0) {
		// 진행 가능한 상담원이 없으면?
			int diff = counsel_q[curr_category - 1].top() - reqs[i][0];
			waiting_time += diff;

			counsel_q[curr_category - 1].pop();
			counsel_q[curr_category - 1].push(reqs[i][0] + reqs[i][1]);
			
			continue;
		}

		counsel_q[curr_category - 1].push(reqs[i][0] + reqs[i][1]);
		counselors[curr_category - 1]--;
	}

	return waiting_time;
}
{% endhighlight %}  

하지만 아쉽게도 절반의 케이스에서 시간초과가 발생했다.  
예제 케이스와 시간초과가 뜨지 않는 케이스는 모두 정답처리가 됐으니,  
핵심 로직은 우선 제쳐두고 시간초과의 원인을 찾도록 해보자.  

### Timeout : Backtracking
처음엔 ```counselors``` 배열이 문제라고 생각했다.  
C++은 배열을 매개변수로 넘기게 되면 함수 내에서 배열을 새로 생성한다.  
그렇기에 보통 ```&``` 연산자를 통해 ```Call by reference```를 수행하는데,  
사실 이는 메모리 초과라고 보는 것이 맞는 것 같아 후보에서 제외했다.  

다음으로 백트래킹 로직을 살펴보았다.  
내 백트래킹 로직을 보면 ```for```문을 통해서 조합을 만들고 있다.  
이 과정에서 문제를 발견할 수 있었다.  
굳이 배열의 처음부터 다시 훑어서 조합을 만들어야 할까?  

순서가 바뀌면 다른 조합이니, 처음부터 훑어야 한다고 생각했다.  
하지만 처음부터 훑는 경우엔 굳이 확인이 필요없는 중복이 생긴다.  
즉, 현재 인덱스를 넘겨 해당 위치부터 훑기만 해도 조합을 만들 수 있다.  
해당 부분이 시간 초과가 발생한 핵심 원인이었다.  

해당 로직을 고치니 곧바로 정답을 받을 수 있었다.  
오랜만에 레벨 3 문제를 혼자 힘으로 해결하니 뿌듯하다.  
