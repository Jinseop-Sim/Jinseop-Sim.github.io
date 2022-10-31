---
layout: post
title: "프로그래머스 Level 3 - 코딩 테스트 공부"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
  math: true
---
- [코딩 테스트 공부[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/118668)  

2022 KAKAO INTERNSHIP 문제였다.  
나에게는 아직 혼자서 생각해내기 매우 어려운 문제였던 것 같다.  
내가 생각했던 점은 아래와 같다.  
- 학습을 하거나 문제를 푸는 경우를 모두 생각해보아야 한다.
  - 즉, 완전 탐색을 해보아야 일단 알 수 있다.

### DP + Recursion
{% highlight cpp %}
#include <iostream>
#include <string>
#include <vector>
using namespace std;
#define INF 100000000

int DP[151][151]; // alp와 cop의 최대값 150
int max_alp = 0, max_cop = 0; // 가장 큰 alp와 cop를 넘어가면 더 이상 탐색할 필요가 없다.
int study(int alp, int cop, vector<vector<int>>& problems) {
    if (alp >= max_alp && cop >= max_cop) return 0;

    if (alp > max_alp) alp = max_alp;
    if (cop > max_cop) cop = max_cop;

    int& res = DP[alp][cop];
    if (res != 0) return res;
    res = INF;

    for (auto vec : problems) {
        if (alp < vec[0] || cop < vec[1]) continue;
        res = min(res, study(alp + vec[2], cop + vec[3], problems) + vec[4]);
    }

    res = min(res, study(alp + 1, cop, problems) + 1);
    res = min(res, study(alp, cop + 1, problems) + 1);

    return res;
}

int solution(int alp, int cop, vector<vector<int>> problems) {
    int answer = 0;
    
    for (auto vec : problems) {
        max_alp = max(max_alp, vec[0]);
        max_cop = max(max_cop, vec[1]);
    }
    
    answer = study(alp, cop, problems);
    return answer;
}
{% endhighlight %}