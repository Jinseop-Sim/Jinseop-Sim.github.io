---
layout: post
title: "프로그래머스 Level 3 - 거스름돈"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [거스름돈[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/12907)  

보자마자 DP로 풀면 될 것이라는 생각이 들었다.  
아래와 같은 이유로 DP로 풀 수 있을 것이라는 생각이 들었다.  

- 1원은 모든 돈을 만들 수 있다.
- 1원으로 만들 수 있는 경우의 수 + 2원의 경우의 수로 새로운 돈을 만들 수 있다.
- N원으로 만들 수 있는 경우의 수 + M원의 경우의 수면 모든 수 K를 만들 수 있다.
  - 그렇다면 이전에 계산한 경우의 수를 더하며 나아가도록 한다!

그럼 어떤 식으로 계산을 진행할 것인가?  
[사진]

### DP
{% highlight cpp %}
#include <string>
#include <vector>
using namespace std;

int DP[100001];
int solution(int n, vector<int> money) {
    int answer = 0;
    DP[0] = 1;

    for (int i = 0; i < money.size(); i++) {
        for (int j = money[i]; j <= n; j++) {
            DP[j] += DP[j - money[i]];
            DP[j] %= 1000000007;
        }
    }

    answer = DP[n];
    return answer;
}
{% endhighlight %}