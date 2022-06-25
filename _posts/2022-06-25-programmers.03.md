---
layout: post
title: "프로그래머스 Level 3 - 다단계 칫솔 판매"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---

## 프로그래머스 Level 3 : 다단계 칫솔 판매

문제는 아래와 같다.  

### 1차 시도 : Adjacent List
처음에 봤을 때, Tree 구조를 만들어야 하는 문제였기 때문에  
인접 리스트로 구현을 해서 DFS를 이용해 풀려고 시도를 했었다.  

{% highlight cpp %}

struct info {
    string name;
    int reven;
};

int convert(string name, vector<string> enroll) {
    for (int i = 0; i < enroll.size(); i++) {
        if (name == enroll[i]) return i+1;
    }
}

void init_list(vector<string> enroll, vector<string> referral, vector<string> seller, vector<int> amount, vector<vector<info>>& multi) {
    for (int i = 0; i < enroll.size(); i++) {
        info temp;
        temp.name = enroll[i]; temp.reven = put_reven(enroll[i], seller, amount);
        if (referral[i] == "-") {
            multi[0].push_back(temp); continue;
        }
        multi[convert(enroll[i], enroll)].push_back(temp);
    }
}

{% endhighlight %}  

하지만, 인접 리스트로 구현을 해서 DFS를 돌려 계산을 하기엔  
너무나도 많은 함수와 조건들을 필요로 해서 시간초과가 날 것 같았다.  

그래서 구글의 힘을 살짝 빌려보니, 다들 Hash map을 이용했음을 알 수 있었다.  
이 문제는 정수로 node 이름을 정하지 않고 String으로 정하고 있기 때문에,  
확실히 Hash map을 이용하면 빠르게 해결이 가능할 것이다.  

### 2차 시도 : Hash map

{% highlight cpp %}

#include <unordered_map>
#include <vector>
#include <iostream>
using namespace std;


unordered_map<string, string> pedigree; // <본인, 추천인> 관계를 저장
unordered_map<string, int> revenue; // <본인, 소득> 관계를 저장

void calc_revenue(string name, int profit) {
    if (name == "minho") return;
    // 추천인이 Center와 같으면 종료.

    int commission = profit * 0.1; // 상납금
    revenue[name] += profit - commission; 
    if (commission < 1) return; // 계산한 값이 1 이하면 상납하지 않는다.

    calc_revenue(pedigree[name], commission);
    // 부모로 쭉쭉 타고 올라가도록 재귀.
}

vector<int> solution(vector<string> enroll, vector<string> referral, vector<string> seller, vector<int> amount) {
    vector<int> answer;

    for (int i = 0; i < referral.size(); i++) {
        if (referral[i] == "-") pedigree[enroll[i]] = "minho";
        else pedigree[enroll[i]] = referral[i];
    } // 추천인 저장.

    for (int i = 0; i < seller.size(); i++)
        calc_revenue(seller[i], amount[i] * 100);

    for (int i = 0; i < enroll.size(); i++)
        answer.push_back(revenue[enroll[i]]);

    return answer;
}

{% endhighlight %}

Hash map의 새로운 용도를 알 수 있는 문제였다.  
Graph(Tree) 문제를 푸는 방식에 대해서 정리를 해야할 것 같다.