---
layout: post
title: "Search : Parametric Search"
categories: Algorithm
tags: [cpp]
author:
  - Jinseop Sim
math: true
toc: true
---
�츮�� ���� �̺� Ž���� �ܼ��� �����̳� �� ã�⿡�� �� �� ������?  
�̺� Ž���� �̿��ϴ� Ž������ __Parametric Search__ �� ���� �˾ƺ���.  

### Parametric Search?
Parametric Search�� ����ȭ ������ �̺� Ž������ Ǯ����� ����̴�.  

> ����ȭ ������ ���� ����(Decision Problem)�� �ٲپ� Ǯ ��.  

����ȭ ������ �̺� Ž���� ���� ������ �������� ���� ã�Ƴ��� ���̴�.  

### Example : ���α׷��ӽ� - �ݰ� �� ����ϱ�
[�ݰ� �� ����ϱ�[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/86053)  

{% highlight cpp %}
#include <string>
#include <vector>
using namespace std;

long long solution(int a, int b, vector<int> g, vector<int> s, vector<int> w, vector<int> t) {
    long long answer = 10e5 * 4 * 10e9;
    // �⺻������ ���� ������ �ִ밪���� �����Ѵ�.

    long long start = 0;
    long long end = 10e5 * 4 * 10e9;
    int truck_num = s.size();

    while (start <= end) {
        long long mid = (start + end) / 2;

        long long gold = 0; // �� ����ؾ��� ��, ��
        long long silver = 0;
        long long carry_sum = 0; // �� ����ؾ��� �ݼ�

        for (int i = 0; i < truck_num; i++) {
            long long curr_gold = (long long)g[i]; // ���� ������ ��, �� �ѵ�
            long long curr_silver = (long long)s[i];
            long long curr_weight = (long long)w[i]; // ���� ������ �ִ� ����
            long long curr_time = (long long)t[i]; // ���� ������ �ҿ� �ð�

            long long how_many_move = mid / (curr_time * 2);
            // �ִ� �ð� �ȿ� ���� ������ �� �� �Դٰ��� �ϴ°�?

            if (mid % (curr_time * 2) >= t[i]) how_many_move++;
            // �Դٰ��� ���� ��, �ð��� ���� ���� ���� �� ���� �����ϴ� ���.

            gold += (curr_gold < how_many_move * curr_weight) ? curr_gold : how_many_move * curr_weight;
            // ���� ������ �� �ѵ��� ���� ��ݰ����� ��ü ���԰� �� ũ��?
            // ���� ������ ���� ����ؾ��� �ݿ� ���Ѵ�.
            // �ƴϸ�, ��� ������ ��ü ���Ը� ���Ѵ�.

            silver += (curr_silver < how_many_move * curr_weight) ? curr_silver : how_many_move * curr_weight;
            // ���� ����.

            carry_sum += (curr_gold + curr_silver < how_many_move * curr_weight) ? curr_gold + curr_silver : how_many_move * curr_weight;
            // ���� ������ ��, �� �ѵ� ���տ� ���� ��ü ��� ���� ���԰� �� ũ��?
            // ���� ������ ��, �� �ѵ� ������ ���ع�����.
            // �ƴϸ�, ��� ������ ���� ��ŭ�� ���Ѵ�.
        }

        if (gold >= a && silver >= b && carry_sum >= a + b) {
            // ��� ��, ���� ��� �����ϴٸ�
            end = mid - 1; // ������ ���������� ���δ�.
            answer = min(mid, answer); // answer�� �� ���������� ����.
        }
        else
            start = mid + 1;
            // ����� �Ұ����� ������� ������ ū ������ ���δ�.
    }

    return answer;
}
{% endhighlight %}