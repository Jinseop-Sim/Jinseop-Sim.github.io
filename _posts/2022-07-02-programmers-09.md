---
layout: post
title: "프로그래머스 Level 2 - 순위 검색"
categories: [KAKAO] Programmers
tags: [cpp]
author:
  - Jinseop Sim
---

- [순위 검색[LEVEL 2]](https://school.programmers.co.kr/learn/courses/30/lessons/72412)   

## 프로그래머스 Level 2 : 순위 검색

분명 문제의 Level은 2인데, 카카오 문제여서 그런지  
체감상 훨씬 더 높은 Level의 문제로 느껴졌다.

### 1차 시도 : 단순 For문

처음엔 Queue와 2차원 배열을 이용해 단순히 For문을 돌려  
Query에 맞는 행만 4번 추려 나가는 식으로 진행하려고 했다.  

Info를 문자열 파싱을 통해 2차원 배열로 만들어 낸 뒤,  
주어지는 Query에 부합하는 행만 추려내는 식이었다.  

하지만 100줄이 넘는 Code를 적다가, 이건 아니라는 생각이 들었다.  
문제를 맞힐 수는 있겠지만, 효율성 면에서 모두 시간초과가 뜰 것이다.  

### 2차 시도 : Hash map과 Bit masking

세상엔 정말 똑똑한 사람이 많은 것 같다.  

문제 해결의 Key는 다음과 같다.
- 문자열 파싱 방식은 동일하다.
- Info를 파싱한 뒤, Map에 Info에서 나올 수 있는 16가지 Query를 모두 넣는다.
  - 이 때 비트 마스킹을 통해서, __"-"__ 를 넣을 자리를 정해준다.
- 각 Map의 Key에 대한 Value가 Score의 Vector로 저장된다.
  - 따라서 그 중에 Query에 해당 되는 값의 수만 세면 된다.

{% highlight cpp %}
#include <string>
#include <vector>
#include <unordered_map>
#include <algorithm>
using namespace std;

vector<string> string_parse(string str) {
    vector<string> res;
    string temp = "";

    for (auto pars : str) {
        if (pars == ' ') {
            if (temp != "and") res.push_back(temp);
            temp = "";
        }
        else temp += pars;
    }

    res.push_back(temp);
    return res;
} // 문자열 파싱 함수, Vector로 반환해준다.

vector<int> solution(vector<string> info, vector<string> query) {
    vector<int> answer;
    unordered_map<string, vector<int>> applicants;

    for (auto data : info) {
        vector<string> key = string_parse(data);
        for (int i = 0; i < 16; i++) {
            string temp = "";
            for (int j = 0; j < 4; j++) {
                if (i & (1 << j)) temp += "-";
                else temp += key[j];
            } // 16가지의 경우를 Map에 넣는 Bit masking 작업
            applicants[temp].push_back(stoi(key[4]));
        }
    }

    for (auto& iter : applicants) sort(iter.second.begin(), iter.second.end());
    // Lower_bound의 이용을 위해 각 Vector 정렬

    for (auto q : query) {
        vector<string> key = string_parse(q);
        string quest = key[0] + key[1] + key[2] + key[3];
        // 파싱한 Query를 Map에서 검색하기 위해 Concate
        vector<int> res = applicants[quest];

        int cnt = res.end() - lower_bound(res.begin(), res.end(), stoi(key[4]));
        // lower_bound를 통해서, Query의 해당 값 이상의 수가
        // 처음 등장하는 index를 반환 받는다.
        // 배열은 반드시 오름차순 정렬이 되어있어야 한다.

        answer.push_back(cnt);
    }

    return answer;
}
{% endhighlight %}