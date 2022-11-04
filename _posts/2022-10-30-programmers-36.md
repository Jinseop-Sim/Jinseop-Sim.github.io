---
layout: post
title: "프로그래머스 Level 3 - 코딩 테스트 공부"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [코딩 테스트 공부[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/118668)  

2022 KAKAO INTERNSHIP 문제였다.  
나에게는 아직 혼자서 생각해내기 매우 어려운 문제였던 것 같다.  
내가 생각했던 점은 아래와 같다.  
- 학습을 하거나 문제를 푸는 경우를 모두 생각해보아야 한다.
  - 즉, 완전 탐색을 해보아야 일단 알 수 있다.
  - 과연 모든 경우를 다 탐색하는데 시간초과가 안날까?

그래서 다른 사람들의 풀이를 참고한 결과, DP를 섞어서 사용하는 것이 열쇠였다.  
완전탐색으로 모든 경우를 탐색을 하되, 배열에 저장을 하면서 나아가는 것이다.  

### DP + 완전탐색
{% highlight cpp %}
#include <iostream>
#include <string>
#include <vector>
using namespace std;
#define INF 100000000

int DP[151][151]; // alp와 cop의 최대값 150
int max_alp = 0, max_cop = 0;
int study(int alp, int cop, vector<vector<int>>& problems) {
    if (alp >= max_alp && cop >= max_cop) return 0;
    // 요구하는 alp와 cop를 넘어가면 더 이상 탐색할 필요가 없다.

    if (alp > max_alp) alp = max_alp;
    if (cop > max_cop) cop = max_cop;
    // 둘 중에 하나가 넘어가버리더라도, 보정을 해주어야 한다.

    int& res = DP[alp][cop]; // 각 점수가 배열 접근의 Key가 된다.
    if (res != 0) return res; // 이미 학습 시간이 있다면 return.
    res = INF; // 학습 시간이 없다면 최대시간으로 늘려놓는다.

    for (auto vec : problems) {
        if (alp < vec[0] || cop < vec[1]) continue;
        // 지금 내 점수가 요구사항보다 낮으면 풀 수 없음

        res = min(res, study(alp + vec[2], cop + vec[3], problems) + vec[4]);
    }
    // 문제를 풀어서 알고력, 코딩력을 보상만큼 올리는 경우

    res = min(res, study(alp + 1, cop, problems) + 1);
    res = min(res, study(alp, cop + 1, problems) + 1);
    // 공부를 해서 알고력, 코딩력을 1씩 올리는 경우

    return res; 
    // 모든 문제르 풀 수 있는 최종 학습시간을 반환한다.
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