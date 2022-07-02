---
layout: post
title: "���α׷��ӽ� Level 2 - ���� �˻�"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---

## ���α׷��ӽ� Level 2 : ���� �˻�

�и� ������ Level�� 2�ε�, īī�� �������� �׷���  
ü���� �ξ� �� ���� Level�� ������ ��������.

### 1�� �õ� : �ܼ� For��

ó���� Queue�� 2���� �迭�� �̿��� �ܼ��� For���� ����  
Query�� �´� �ุ 4�� �߷� ������ ������ �����Ϸ��� �ߴ�.  

Info�� ���ڿ� �Ľ��� ���� 2���� �迭�� ����� �� ��,  
�־����� Query�� �����ϴ� �ุ �߷����� ���̾���.  

������ 100���� �Ѵ� Code�� ���ٰ�, �̰� �ƴ϶�� ������ �����.  
������ ���� ���� �ְ�����, ȿ���� �鿡�� ��� �ð��ʰ��� �� ���̴�.  

### 2�� �õ� : Hash map�� Bit masking

���� ���� �ȶ��� ����� ���� �� ����.  

���� �ذ��� Key�� ������ ����.
- ���ڿ� �Ľ� ����� �����ϴ�.
- Info�� �Ľ��� ��, Map�� Info���� ���� �� �ִ� 16���� Query�� ��� �ִ´�.
  - �� �� ��Ʈ ����ŷ�� ���ؼ�, __"-"__ �� ���� �ڸ��� �����ش�.
- �� Map�� Key�� ���� Value�� Score�� Vector�� ����ȴ�.
  - ���� �� �߿� Query�� �ش� �Ǵ� ���� ���� ���� �ȴ�.

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
} // ���ڿ� �Ľ� �Լ�, Vector�� ��ȯ���ش�.

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
            } // 16������ ��츦 Map�� �ִ� Bit masking �۾�
            applicants[temp].push_back(stoi(key[4]));
        }
    }

    for (auto& iter : applicants) sort(iter.second.begin(), iter.second.end());
    // Lower_bound�� �̿��� ���� �� Vector ����

    for (auto q : query) {
        vector<string> key = string_parse(q);
        string quest = key[0] + key[1] + key[2] + key[3];
        // �Ľ��� Query�� Map���� �˻��ϱ� ���� Concate
        vector<int> res = applicants[quest];

        int cnt = res.end() - lower_bound(res.begin(), res.end(), stoi(key[4]));
        // lower_bound�� ���ؼ�, Query�� �ش� �� �̻��� ����
        // ó�� �����ϴ� index�� ��ȯ �޴´�.
        // �迭�� �ݵ�� �������� ������ �Ǿ��־�� �Ѵ�.

        answer.push_back(cnt);
    }

    return answer;
}
{% endhighlight %}