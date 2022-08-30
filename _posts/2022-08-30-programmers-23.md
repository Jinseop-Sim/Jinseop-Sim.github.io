---
layout: post
title: "프로그래머스 Level 2 - 두 큐 합 같게 만들기"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [두 큐 합 같게 만들기[LEVEL 2]](https://school.programmers.co.kr/learn/courses/30/lessons/118667)  

KAKAO 2022 TECH INTERNSHIP의 문제였다.  
문제 난이도는 그렇게 어려워 보이지 않았으나, 최적화가 힘들었다.  

### 1차 시도 : Vector와 Pointer
실제로 Queue를 이용해서 합을 일일히 계산하려면 시간이 많이 걸릴 것이라는 판단.  
그래서 Vector에 그대로 다 넣으면서 가리키는 포인터만 옮겨주는 방식으로 구현했다.  

하지만 이 방식 또한 5개 정도의 시간초과가 발생했다.  

{% highlight cpp %}
#include <string>
#include <vector>
using namespace std;

bool validate(vector<int> queue1, vector<int> queue2) {
    long long s1 = 0, s2 = 0;
    for (auto i : queue1)
        s1 += i;
    for (auto i : queue2)
        s2 += i;

    if ((s1 + s2) % 2 != 0) return false;
    return true;
}

int calc_rotate(vector<int> queue1, vector<int> queue2) {
    int cnt = 0, ptr_1 = 0, ptr_2 = 0, q1_size = queue1.size(), q2_size = queue2.size();
    long long sum_1 = 0, sum_2 = 0;

    while (true) {
        sum_1 = 0; sum_2 = 0;

        for (int i = ptr_1; i < queue1.size(); i++)
            sum_1 += queue1[i];
        for (int i = ptr_2; i < queue2.size(); i++)
            sum_2 += queue2[i];

        if (sum_1 > sum_2) {
            queue2.push_back(queue1[ptr_1]);
            ptr_1++; cnt++;
        }
        else if (sum_1 < sum_2) {
            queue1.push_back(queue2[ptr_2]);
            ptr_2++; cnt++;
        }

        if (sum_1 == sum_2) break;
        if (cnt > (q1_size + q2_size)*2) return -1;
    }

    return cnt;
}

int solution(vector<int> queue1, vector<int> queue2) {
    int answer = -2;

    if (!validate(queue1, queue2)) answer = -1;
    else answer = calc_rotate(queue1, queue2);

    return answer;
}
{% endhighlight %}


### 2차 시도
코드를 보다보니 내가 코드를 잘못 짰음을 금세 깨달을 수 있었다.  
굳이 while loop 안에서 합을 계속 구할 필요가 있는가?  
초기 합은 구해놓고, 옮긴 놈은 빼고 받은 놈은 더하기만 하면 된다!  

{% highlight cpp %}
#include <string>
#include <vector>
using namespace std;

bool validate(vector<int> queue1, vector<int> queue2) {
    long long s1 = 0, s2 = 0;
    for (auto i : queue1)
        s1 += i;
    for (auto i : queue2)
        s2 += i;

    if ((s1 + s2) % 2 != 0) return false;
    return true;
}

int calc_rotate(vector<int> queue1, vector<int> queue2) {
    int cnt = 0, ptr_1 = 0, ptr_2 = 0, q1_size = queue1.size(), q2_size = queue2.size();
    long long sum_1 = 0, sum_2 = 0;

    for (int i = ptr_1; i < queue1.size(); i++)
        sum_1 += queue1[i];
    for (int i = ptr_2; i < queue2.size(); i++)
        sum_2 += queue2[i];

    while (true) {
        if (sum_1 > sum_2) {
            sum_1 -= queue1[ptr_1];
            queue2.push_back(queue1[ptr_1]);
            sum_2 += queue2[queue2.size() - 1];
            ptr_1++; cnt++;
        }
        else if (sum_1 < sum_2) {
            sum_2 -= queue2[ptr_2];
            queue1.push_back(queue2[ptr_2]);
            sum_1 += queue1[queue1.size() - 1];
            ptr_2++; cnt++;
        }

        if (sum_1 == sum_2) break;
        if (cnt > (q1_size + q2_size) * 2) return -1;
    }

    return cnt;
}

int solution(vector<int> queue1, vector<int> queue2) {
    int answer = -2;

    if (!validate(queue1, queue2)) answer = -1;
    else answer = calc_rotate(queue1, queue2);

    return answer;
}
{% endhighlight %}