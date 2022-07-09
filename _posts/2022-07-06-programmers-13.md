---
layout: post
title: "프로그래머스 Level 2 - 위장"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [위장[LEVEL 2]](https://school.programmers.co.kr/learn/courses/30/lessons/42578)  

의외의 복병 문제였다.  

### 1차 시도 : 백트래킹
너무 생각없이 문제에 접근했다.  
Map을 이용해서 옷 종류를 받아온 것은 올바른 접근이었다.  
하지만 백트래킹을 해버려서 쓸데없이 시간초과가 발생했다.  

{% highlight cpp %}
#include <vector>
#include <unordered_map>
#include <iostream>
using namespace std;

unordered_map<string, int> table;
vector<int> values;
int cnt = 1, sum = 0;

void clear_visit(vector<bool>& visit) {
    for (int i = 0; i < visit.size(); i++)
        visit[i] = false;
}

void make_value_table(vector<vector<string>> clothes) {
    for (auto i : clothes)
        table[i[1]]++;
    for (auto i : table)
        values.push_back(i.second);
}

void backtrack(int start, int depth, int limit, vector<bool>& visit) {
    if (depth == limit) {
        sum += cnt;
        return;
    }

    for (int i = start; i < values.size(); i++) {
        if (!visit[i]) {
            cnt *= values[i];
            visit[i] = true;
            backtrack(i, depth + 1, limit, visit);
            cnt /= values[i];
            visit[i] = false;
        }
    }
}

int solution(vector<vector<string>> clothes) {
    int answer = clothes.size();

    make_value_table(clothes);
    vector<bool> visit(values.size(), 0);

    for (int i = 2; i < table.size()+1; i++) {
        backtrack(0, 0, i, visit);
        clear_visit(visit);
    }

    answer += sum;
    return answer;
}

int main() {
    vector<vector<string>> clothes = { {"yellow_hat", "headgear"}
        ,{"blue_sunglasses", "eyewear"},{"green_turban", "headgear"}};

    cout << solution(clothes);
    return 0;
}
{% endhighlight %}

### 2차 시도 : 수학적으로 접근하기

수학적인 조합으로 문제를 풀어보았다.  

{% highlight cpp %}
#include <vector>
#include <unordered_map>
#include <iostream>
using namespace std;

unordered_map<string, int> table;
int cnt = 1, sum = 0;

void make_table(vector<vector<string>> clothes) {
    for (auto i : clothes)
        table[i[1]]++;
}

int solution(vector<vector<string>> clothes) {
    int answer = 1;

    make_table(clothes);
    for (auto i : table) {
        answer *= i.second + 1;
    }

    return answer - 1;
}
{% endhighlight %}