---
layout: post
title: "���α׷��ӽ� Level 2 - �ĺ�Ű"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---

## ���α׷��ӽ� Level 2 : �ĺ�Ű

2019�� īī�� �ڵ��׽�Ʈ ��������.  
�������� ����Ұ� ���� ��������.  

���� ����ؾ� �ߴ� ������ �Ʒ��� ����.  
1. �������� ��� Key�� ������ �� ¥����.
  - ��� ������ ���� �ڽ��� ����, ���� DFS�� ���.
2. �ĺ�Ű�� ���ϱ� ���� ���ϼ�(Uniqueness)�� �˻��Ѵ�.
3. ������ �ĺ�Ű�� ��������, �� �ĺ�Ű�� ���ԵǴ� Ű�� ���տ� �� �� ����.
  - �� �ּҼ�(Minimality) �κ��� �����ϴ� ���� ���� �������.
  - ǥ�� ũ�Ⱑ �� ���� �ʾұ� ������, ���� ����Ž������ �˻��ߴ�.

- ���ϼ� �˻�� ��� �����ϴ°�?
  - �ߺ��� ������� �ʴ�, Unordered set�� �̿��ߴ�.
    - �ڵ� ������ �Ǵ� Set���� �� ���� Unordered�� ���.
  - �ߺ��� ���ٸ�, set�� ���� �� ������ ���� ��!
    - �� ���� Column ������ �ĺ�Ű�� �ȴ�.
    - �� �ĺ�Ű ���յ��� Vector�� ��� Push back.

- �ּҼ� �˻�� ��� �����ϴ°�?
  - �ĺ�Ű ������ ���� Vector�� ���� �˻����� ������ ����Ž���Ѵ�.
    - ex) �ĺ�Ű ���� <0, 13> | ���� �˻����� ���� 123  
          0�� 123�� �˻��ؼ� ��ġ�� �κ� ����. ���.  
          13�� 123�� �˻����� ��, 13�� 123�� ���ԵǹǷ� ����.  
          �ּҼ� �˻翡�� Ż��!  

{% highlight cpp %}
#include <string>
#include <vector>
#include <unordered_set>
using namespace std;

vector<string> combs;
vector<string> res;

void make_comb(int start, int depth, int limit, string comb, vector<bool> visit) {
    if (depth == limit) {
        combs.push_back(comb);
        return;
    }

    for (int i = start; i < visit.size(); i++) {
        if (!visit[i]) {
            visit[i] = true;
            comb += to_string(i);
            make_comb(i, depth + 1, limit, comb, visit);
            comb.erase(comb.size() - 1);
        }
    }
} // ��� ������ ����� DFS

bool check_min(string comb) {
    for (int i = 0; i < res.size(); i++) {
        int cnt = 0;
        for (int j = 0; j < res[i].size(); j++) {
            for (int k = 0; k < comb.size(); k++) {
                if (res[i][j] == comb[k]) cnt++;
                if (cnt == res[i].size()) return false;
            }
        }
    }

    return true;
} // �ּҼ� �˻� �Լ�
 // �ĺ�Ű vector�� res�� ���� ���� comb�� ����Ž��.

void check_unique(vector<vector<string>> relation, string comb) {
    unordered_set<string> classifier;
    
    if (!check_min(comb)) return;

    for (int i = 0; i < relation.size(); i++) {
        string temp = "";
        for (int j = 0; j < comb.size(); j++)
            temp += relation[i][comb[j] - '0'];
        classifier.insert(temp);
    }
    
    if (classifier.size() == relation.size()) {
        res.push_back(comb);
    }
} // ���ϼ� �˻� �Լ�
 // ���ϼ��� �ߺ��� �㰡���� �ʴ� Unordered Set �̿�.

int solution(vector<vector<string>> relation) {
    int answer = 0;
    string comb = "";
    vector<bool> visit(relation[0].size(), 0);


    for (int i = 1; i < relation[0].size() + 1; i++) {
        make_comb(0, 0, i, comb, visit); // 1 ~ �� ���� ��ŭ ��� ����
        for (int i = 0; i < combs.size(); i++)
            check_unique(relation, combs[i]);
        combs.clear(); // ����־�� �Ѵ�.
    }

    answer = res.size();
    return answer;
}
{% endhighlight %}