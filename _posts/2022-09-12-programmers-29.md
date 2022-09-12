---
layout: post
title: "프로그래머스 Level 2 - 양궁 대회"
categories: KAKAO Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [양궁 대회[LEVEL 2]](https://school.programmers.co.kr/learn/courses/30/lessons/92342)  

2022 KAKAO BLIND RECURITMENT의 문제였다.  
아마 문제 난이도는 그렇게 어렵지 않은 것 같은데 구현하기가 어려웠다.  

당연히 DFS로 구현을 해야한다고 생각은 했었지만, 조건을 만족시키는데에 어려움이 있었다.  
처음엔 아예 완전탐색으로 구현을 했지만 시간 초과가 날 것 같아서 포기했다.  
여러 시도 끝에 해설을 참고하여 풀어보았다.  

생각해야 할 관점은 아래와 같다.  
1. 가장 큰 점수 차를 벌려야 하므로, 쓸데없는 곳에 화살을 낭비하지 않는다.
  - 합리적으로 화살을 쏘기 위해 Ryan = Apeach + 1 만 쏘고 넘어가자!
  - 만약 남은 화살이 Apeach + 1보다 작거나 같다면, 쏠 필요없이 다음 점수로 넘어가자.
2. Tie Break일 때는 가장 작은 점수부터 채우도록 한다. __(핵심!)__
  - 작은 Index부터 이전 답과 현재 답을 비교해서 낮은 쪽 분포가 많은 쪽으로 답을 선택.

__ 반드시 한 번 더 풀어볼 것 !!!__  

### DFS with Condition
{% highlight cpp %}
#include <iostream>
#include <vector>
using namespace std;

int max_diff = 0;
vector<int> res;
bool validate(vector<int> ryan) {
    /* 뒤에서 부터 탐색하는 이유는
     더 작은 점수에 더 많은 화살을 쐈음을 확인하기 위함.
     그 이유는 Tie break 때문이다. */ 
    for (int i = 10; i >= 0; i--) {
        if (ryan[i] > res[i]) 
            return true;
        else if (ryan[i] < res[i]) 
            return false;
    }
}

void calc_score_sum(vector<int> ryan, vector<int> apeach) {
    int ryan_sum = 0;
    int apeach_sum = 0;

    for (int i = 0; i < 11; i++) {
        if (ryan[i] > apeach[i]) ryan_sum += (10 - i);
        else if (apeach[i] != 0) apeach_sum += (10 - i);
    }

    int diff = ryan_sum - apeach_sum; // 둘의 차이
    if (diff > 0 && max_diff <= diff) {
        if (max_diff == diff && !validate(ryan)) return;
        /* 같은 점수(Tie break) 일 때, 점수가 낮은 곳에 몰려있는지 확인.
           낮은 곳에 모여있지 않는다면, 답으로 채용할 필요가 없다.*/
        max_diff = diff;
        res = ryan;
    }
}

void dfs(int idx, int remainder, vector<int>& ryan, vector<int> apeach) {
    if (idx == 11 || remainder == 0) { // 화살을 모두 쐈을 때
        ryan[10] += remainder;
        // 남은 화살은 가장 낮은 점수에 모두 부여
        calc_score_sum(ryan, apeach); // 합 계산
        ryan[10] -= remainder;
        return;
    }

    if (apeach[idx] < remainder) {
        // 어피치가 해당 점수에 쏜 화살보다 남은 화살이 많을 때.
        ryan[idx] += (apeach[idx] + 1);
        // 화살을 가능한 만큼 쏜 후 다음으로
        dfs(idx + 1, remainder - (apeach[idx] + 1), ryan, apeach);
        ryan[idx] -= (apeach[idx] + 1);
    }
    
    dfs(idx + 1, remainder, ryan, apeach);
    /* 해당 점수에 쏜 화살보다 많이 남지 않았거나 같은 경우엔 쏠 필요 X(낭비)
       그냥 다음 Index로 넘어간다. */
}

vector<int> solution(int n, vector<int> info) {
    vector<int> answer;
    vector<int> ryan(11, 0);

    dfs(0, n, ryan, info);
    if (res.empty()) answer.push_back(-1);
    else answer = res;

    return answer;
}
{% endhighlight %}