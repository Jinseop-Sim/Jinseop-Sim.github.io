---
layout: post
title: "���α׷��ӽ� Level 3 - �ٴܰ� ĩ�� �Ǹ�"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---

## ���α׷��ӽ� Level 3 : �ٴܰ� ĩ�� �Ǹ�

������ �Ʒ��� ����.  

### 1�� �õ� : Adjacent List
ó���� ���� ��, Tree ������ ������ �ϴ� �������� ������  
���� ����Ʈ�� ������ �ؼ� DFS�� �̿��� Ǯ���� �õ��� �߾���.  

{% highlight cpp %}

struct info {
    string name;
    int reven;
};

int convert(string name, vector<string> enroll) {
    for (int i = 0; i < enroll.size(); i++) {
        if (name == enroll[i]) return i+1;
    }
}

void init_list(vector<string> enroll, vector<string> referral, vector<string> seller, vector<int> amount, vector<vector<info>>& multi) {
    for (int i = 0; i < enroll.size(); i++) {
        info temp;
        temp.name = enroll[i]; temp.reven = put_reven(enroll[i], seller, amount);
        if (referral[i] == "-") {
            multi[0].push_back(temp); continue;
        }
        multi[convert(enroll[i], enroll)].push_back(temp);
    }
}

{% endhighlight %}  

������, ���� ����Ʈ�� ������ �ؼ� DFS�� ���� ����� �ϱ⿣  
�ʹ����� ���� �Լ��� ���ǵ��� �ʿ�� �ؼ� �ð��ʰ��� �� �� ���Ҵ�.  

�׷��� ������ ���� ��¦ ��������, �ٵ� Hash map�� �̿������� �� �� �־���.  
�� ������ ������ node �̸��� ������ �ʰ� String���� ���ϰ� �ֱ� ������,  
Ȯ���� Hash map�� �̿��ϸ� ������ �ذ��� ������ ���̴�.  

### 2�� �õ� : Hash map

{% highlight cpp %}

#include <unordered_map>
#include <vector>
#include <iostream>
using namespace std;


unordered_map<string, string> pedigree; // <����, ��õ��> ���踦 ����
unordered_map<string, int> revenue; // <����, �ҵ�> ���踦 ����

void calc_revenue(string name, int profit) {
    if (name == "minho") return;
    // ��õ���� Center�� ������ ����.

    int commission = profit * 0.1; // �󳳱�
    revenue[name] += profit - commission; 
    if (commission < 1) return; // ����� ���� 1 ���ϸ� ������ �ʴ´�.

    calc_revenue(pedigree[name], commission);
    // �θ�� ���� Ÿ�� �ö󰡵��� ���.
}

vector<int> solution(vector<string> enroll, vector<string> referral, vector<string> seller, vector<int> amount) {
    vector<int> answer;

    for (int i = 0; i < referral.size(); i++) {
        if (referral[i] == "-") pedigree[enroll[i]] = "minho";
        else pedigree[enroll[i]] = referral[i];
    } // ��õ�� ����.

    for (int i = 0; i < seller.size(); i++)
        calc_revenue(seller[i], amount[i] * 100);

    for (int i = 0; i < enroll.size(); i++)
        answer.push_back(revenue[enroll[i]]);

    return answer;
}

{% endhighlight %}

Hash map�� ���ο� �뵵�� �� �� �ִ� ��������.  
Graph(Tree) ������ Ǫ�� ��Ŀ� ���ؼ� ������ �ؾ��� �� ����.