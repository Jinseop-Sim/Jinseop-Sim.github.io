---
layout: post
title: "프로그래머스 Level 2 - 후보키"
categories: [KAKAO] Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [후보키[LEVEL 2]](https://school.programmers.co.kr/learn/courses/30/lessons/42890)   

2019년 카카오 코딩테스트 문제였다.  
생각보다 고려할게 많은 문제였다.  

내가 고려해야 했던 조건은 아래와 같다.  
1. 조합으로 모든 Key의 조합을 다 짜본다.
  - 어떻게 구현할 지는 자신의 선택, 나는 DFS를 썼다.
2. 후보키로 정하기 위해 유일성(Uniqueness)을 검사한다.
3. 유일한 후보키가 정해지면, 그 후보키가 포함되는 키는 조합에 낄 수 없다.
  - 이 최소성(Minimality) 부분을 구현하는 것이 가장 어려웠다.
  - 표의 크기가 얼마 되지 않았기 때문에, 나는 완전탐색으로 검사했다.

- 유일성 검사는 어떻게 진행하는가?
  - 중복을 허용하지 않는, Unordered set을 이용했다.
    - 자동 정렬이 되는 Set보다 더 빨라서 Unordered를 사용.
  - 중복이 없다면, set과 남은 행 갯수가 같을 것!
    - 그 때의 Column 조합이 후보키가 된다.
    - 그 후보키 조합들을 Vector에 모두 Push back.

- 최소성 검사는 어떻게 진행하는가?
  - 후보키 조합이 모인 Vector와 현재 검사중인 조합을 완전탐색한다.
    - ex) 후보키 조합 <0, 13> | 현재 검사중인 조합 123  
    - 0과 123을 검사해서 겹치는 부분 없음. 통과.  
    - 13과 123을 검사했을 때, 13은 123에 포함되므로 불통.  
    - 최소성 검사에서 탈락!  

{% highlight cpp %}
#include <string>
#include <vector>
#include <unordered_set>
using namespace std;

vector<string> combs;
vector<string> res;

void make_comb(int start, int depth, int limit, string comb, vector<bool> visit) {
    if (depth == limit) {
        combs.push_back(comb);
        return;
    }

    for (int i = start; i < visit.size(); i++) {
        if (!visit[i]) {
            visit[i] = true;
            comb += to_string(i);
            make_comb(i, depth + 1, limit, comb, visit);
            comb.erase(comb.size() - 1);
        }
    }
} // 모든 조합을 만드는 DFS

bool check_min(string comb) {
    for (int i = 0; i < res.size(); i++) {
        int cnt = 0;
        for (int j = 0; j < res[i].size(); j++) {
            for (int k = 0; k < comb.size(); k++) {
                if (res[i][j] == comb[k]) cnt++;
                if (cnt == res[i].size()) return false;
            }
        }
    }

    return true;
} // 최소성 검사 함수
 // 후보키 vector인 res와 현재 조합 comb를 완전탐색.

void check_unique(vector<vector<string>> relation, string comb) {
    unordered_set<string> classifier;
    
    if (!check_min(comb)) return;

    for (int i = 0; i < relation.size(); i++) {
        string temp = "";
        for (int j = 0; j < comb.size(); j++)
            temp += relation[i][comb[j] - '0'];
        classifier.insert(temp);
    }
    
    if (classifier.size() == relation.size()) {
        res.push_back(comb);
    }
} // 유일성 검사 함수
 // 유일성은 중복을 허가하지 않는 Unordered Set 이용.

int solution(vector<vector<string>> relation) {
    int answer = 0;
    string comb = "";
    vector<bool> visit(relation[0].size(), 0);


    for (int i = 1; i < relation[0].size() + 1; i++) {
        make_comb(0, 0, i, comb, visit); // 1 ~ 열 갯수 만큼 모든 조합
        for (int i = 0; i < combs.size(); i++)
            check_unique(relation, combs[i]);
        combs.clear(); // 비워주어야 한다.
    }

    answer = res.size();
    return answer;
}
{% endhighlight %}