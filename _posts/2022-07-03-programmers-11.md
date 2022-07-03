---
layout: post
title: "프로그래머스 Level 2 - 후보키"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---

2020 카카오 인턴십 문제였다.  

### 1차 시도 : Unordered Set과 완전 탐색

처음엔 Unordered Set에 집어 넣은 뒤, 종류의 수를 확인하며 진행했다.  
완전탐색에 가깝게 진행을 하기 때문에 시간초과가 다량 발생했다.  
Unordered Set의 삽입, 제거 시간 복잡도는 O(1)이라 괜찮을 줄 알았는데 아니었다.  

{% highlight cpp %}
#include <string>
#include <vector>
#include <unordered_set>
using namespace std;

unordered_set<string> classifier;
pair<int, int> ans;

int how_many_kind(vector<string> gems) {
    for (auto i : gems)
        classifier.insert(i);
    return classifier.size();
}

void is_there_all(vector<string> gems) {
    int kind_of = how_many_kind(gems);
    int point_st = 0, point_en = 1, min_dist = 100001;
    classifier.clear();

    while (true) {
        if (point_st > gems.size() - kind_of) break;
        bool is_find = false;
        for (int i = point_st; i < point_en; i++) {
            classifier.insert(gems[i]);
            if (classifier.size() == kind_of) {
                int dist = point_en - point_st;
                if (min_dist > dist) {
                    min_dist = dist;
                    ans.first = point_st;
                    ans.second = point_en;
                    is_find = true;
                }
            }
        }

        if (is_find || point_en == gems.size()) {
            point_st++;
            point_en = 1;
            classifier.clear();
        }
        point_en++;
    }
}

vector<int> solution(vector<string> gems) {
    vector<int> answer;

    is_there_all(gems);
    answer.push_back(ans.first+1);
    answer.push_back(ans.second);

    return answer;
}
{% endhighlight %}