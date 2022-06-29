---
layout: post
title: "프로그래머스 Level 3 - 셔틀 버스"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---

## 프로그래머스 Level 3 : 셔틀 버스

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
}

vector<pair<string, int>> set_bus(int n, int t, int m) {
    vector<pair<string, int>> table; 
    int hour = 9;
    int min = 0;
    string bt_s = "09:00";

    table.push_back(make_pair(bt_s, m));
    for (int i = 0; i < n - 1; i++) {
        min += t;
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
}

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
                wait_q.pop();
                bus[i].second--;
            }
            else break;
        }
    }

    if (bus[bus.size() - 1].second == 0)
        no_seat = true;

    if (no_seat) answer = convert_time(answer);
    else answer = bus[bus.size() - 1].first;

    return answer;
}

int main() {
    vector<string> timetable = { "09:10", "09:09", "08:00" };

    init_queue(timetable);
    cout << solution(2, 10, 2, timetable);

    return 0;
}
{% endhighlight %}