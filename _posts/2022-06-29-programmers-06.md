---
layout: post
title: "프로그래머스 Level 3 - 셔틀 버스"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---

- [셔틀 버스[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/17678)  

KAKAO BLIND 공채의 문제였다.  
저번 자물쇠와 열쇠보다는 나았던 것 같다.  

내가 생각한 풀이의 Key는 다음과 같다.  
1. 주어진 n(버스 수)과 t(간격)를 이용해 버스 시간표를 만든다.
  - 이 때, String과 Int의 Pair로 만들며, Int에는 남은 자리 수가 들어간다.
2. 그 버스 시간표를 순회하며 사람들을 차례대로 태운다.
3. 남는 자리 시간 확인은 버스의 마지막 시간대만 하면 된다!
  - 왜? 가장 늦게 타는 시간을 계산하는 것이기 때문에.
  - 마지막 시간대에 자리가 있다면? 그냥 그 시간에 탑승.
  - 마지막 시간대에 자리가 없다면? 제일 늦게 타는 사람 - 1분에 탄다!

{% highlight cpp %}
#include <iostream>
#include <vector>
#include <string>
#include <queue>
using namespace std;

priority_queue<string, vector<string>, greater<string>> wait_q;
int idx = 0;
bool no_seat = false;

void init_queue(vector<string> timetable) {
    for (int i = 0; i < timetable.size(); i++)
        wait_q.push(timetable[i]);
} // Queue에 대기 중인 사람들 삽입

vector<pair<string, int>> set_bus(int n, int t, int m) {
    // 버스 시간표를 만드는 함수
    vector<pair<string, int>> table; 
    int hour = 9;
    int min = 0;
    string bt_s = "09:00";

    table.push_back(make_pair(bt_s, m));
    for (int i = 0; i < n - 1; i++) {
        min += t; // t분 간격으로 bus가 온다.
        if (min > 59) {
            hour += 1;
            min -= 60;
        }
        bt_s = to_string(hour / 10) + to_string(hour % 10) + ":"
            + to_string(min / 10) + to_string(min % 10);
        table.push_back(make_pair(bt_s, m));
    }

    return table;
}

string convert_time(string t) {
    int hour = (t[0] - '0') * 10 + (t[1] - '0');
    int min = (t[3] - '0') * 10 + (t[4] - '0');
    
    min -= 1;
    if (min < 0) {
        hour -= 1;
        min += 60;
    }
    t = to_string(hour / 10) + to_string(hour % 10) + ":"
        + to_string(min / 10) + to_string(min % 10);
    return t;
} // 내가 넣은 시간을 1분 줄여주는 함수

string solution(int n, int t, int m, vector<string> timetable) {
    vector<pair<string, int>> bus = set_bus(n, t, m);
    int idx = 0;
    bool no_seat = false;
    string answer = "";

    for (int i = 0; i < bus.size(); i++) {
        if (wait_q.empty()) break;
        for (int j = 0; j < m; j++) {
            if (wait_q.empty()) break;
            if (bus[i].first >= wait_q.top()) {
                answer = wait_q.top();
                // Answer을 계속 queue의 top으로 초기화.
                // 그러면 마지막에 버스 탄 사람이 마지막에 저장됨.
                wait_q.pop();
                bus[i].second--;
                // 빠른 시간 순서로 Queue에서 뽑으면서,
                // Bus의 자리를 채운다.
            }
            else break;
        }
    }

    if (bus[bus.size() - 1].second == 0)
        no_seat = true;
        // 마지막 시간대 버스에 자리가 있는지 확인.

    if (no_seat) answer = convert_time(answer);
    // 자리가 없으면, 마지막에 탄 사람보다 1분 빠르게 탐.
    else answer = bus[bus.size() - 1].first;
    // 자리가 있으면 그냥 마지막 시간대에 탐.

    return answer;
}

int main() {
    vector<string> timetable = { "09:10", "09:09", "08:00" };

    init_queue(timetable);
    cout << solution(2, 10, 2, timetable);

    return 0;
}
{% endhighlight %}

### 생각해 볼 점

- 마지막 시간대만 확인을 하면 되는데, 굳이 Queue를 다 돌아야 할까?
    - 분명 마지막 시간대의 자리만 확인하도록 최적화가 가능할 것이다.
- 버스 시간표가 굳이 필요할까?
    - 버스 시간을 매번 계산해서 사용하면 공간이 절약될 것이다.