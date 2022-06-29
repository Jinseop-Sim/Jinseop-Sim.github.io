---
layout: post
title: "���α׷��ӽ� Level 3 - ��Ʋ ����"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---

## ���α׷��ӽ� Level 3 : ��Ʋ ����

KAKAO BLIND ��ä�� ��������.  
���� �ڹ���� ���躸�ٴ� ���Ҵ� �� ����.  

���� ������ Ǯ���� Key�� ������ ����.  
1. �־��� n(���� ��)�� t(����)�� �̿��� ���� �ð�ǥ�� �����.
  - �� ��, String�� Int�� Pair�� �����, Int���� ���� �ڸ� ���� ����.
2. �� ���� �ð�ǥ�� ��ȸ�ϸ� ������� ���ʴ�� �¿��.
3. ���� �ڸ� �ð� Ȯ���� ������ ������ �ð��븸 �ϸ� �ȴ�!
  - ��? ���� �ʰ� Ÿ�� �ð��� ����ϴ� ���̱� ������.
  - ������ �ð��뿡 �ڸ��� �ִٸ�? �׳� �� �ð��� ž��.
  - ������ �ð��뿡 �ڸ��� ���ٸ�? ���� �ʰ� Ÿ�� ��� - 1�п� ź��!

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