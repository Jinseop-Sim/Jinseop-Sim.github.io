---
layout: post
title: "프로그래머스 Level 3 - 보석 쇼핑"
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

### 2차 시도 : 투 포인터 알고리즘

결국 알고리즘이 떠오르지 않아 다른 분들의 풀이를 참고했다.  
투 포인터 알고리즘을 사용하는 문제였다.  
투 포인터 알고리즘에 대한 공부를 하도록 하자.  

{% highlight cpp %}
#include <string>
#include <vector>
#include <unordered_set>
#include <unordered_map>
using namespace std;

unordered_set<string> kinder;
unordered_map<string, int> classifier;
pair<int, int> ans;

int how_many_kind(vector<string> gems) {
    for (auto i : gems)
        kinder.insert(i);
    return kinder.size();
}

void is_there_all(vector<string> gems) {
    int kind_of = how_many_kind(gems);
    int point_st = 0, point_en = 0;

    for (auto gem : gems) {
        classifier[gem]++;
        if (classifier.size() == kind_of) break;
        point_en++;
    }
    
    int min_dist = point_en - point_st;
    ans.first = point_st + 1;
    ans.second = point_en + 1;

    while (point_en < gems.size()) {
        string cursor = gems[point_st];
        classifier[cursor]--; 
        point_st++; // 시작 포인터를 앞으로 옮김

        if (classifier[cursor] == 0) { // 없는 종류가 생기면
            point_en++; // 끝 포인터를 뒤로 옮김
            for (; point_en < gems.size(); point_en++) {
                classifier[gems[point_en]]++;
                if (cursor == gems[point_en]) break; // 없는 종류를 만나면 break;
            }
            if (point_en == gems.size()) break; // 마지막까지 가면 break
        }

        if (min_dist > (point_en - point_st)) {
            ans.first = point_st + 1;
            ans.second = point_en + 1;
            min_dist = point_en - point_st;
        }
    }
}

vector<int> solution(vector<string> gems) {
    vector<int> answer;

    is_there_all(gems);
    answer.push_back(ans.first);
    answer.push_back(ans.second);

    return answer;
}
{% endhighlight %}