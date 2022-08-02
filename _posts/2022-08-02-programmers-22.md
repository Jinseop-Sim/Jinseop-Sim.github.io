---
layout: post
title: "���α׷��ӽ� Level 3 - ���� ���"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [���� ���[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/43164)

ó���� ������ DFS������� �����ϰ� �����ߴ�.  
�ܼ��� ���ĺ� �����θ� �̾Ƽ� ��θ� ����ϸ� �ȴٰ� �����ؼ�,  
```unordered_map```�� ���� ��, Value�� ```priority_queue```�� �־���.  
���ĺ� ���� ������ ���� �������� ã��, ���� �������� ���� �ȵǹǷ�, Queue���� Pop���ش�.  

������ �� ����� � Case�� ������ ������ ����̾���.  
�ٷ� ���ĺ� ������ ���� ��, �������� ������ �� ���� ����� �� �ִ� Case�̴�.  

__���� ������ ��ΰ� 2�� �̻��� ��� ���ĺ� ������ �ռ��� ��θ� return �մϴ�.__  
���� ���� ������ �־��µ�, �� ������ ��ΰ� ���ٸ� ������ ����� ���ٴ� ���̴�.  

�׷� ```visit``` �迭�� ���� Backtracking�� �ʿ��ϴٰ� �����µ�,  
��� ��ΰ� �߸��Ǿ��� ���� �ڷ� ���ư� �� ������ ���� �ʾҴ�.  

### Ǯ�� : ���Ǻ� Backtracking

�ᱹ �ٸ� �е��� Ǯ�̸� �����غ���, �Լ��� ��ȯ���� ```Bool```�� �δ���.  
�׷� for���� �� ���Ƶ� �� �̻� �� �� �ִ� ���� ������, Backtrack �ϴ� ���̴�.  
�׷��� ���̰� ���� ��� �ִ� Ticket�� ���� �������� True�� Return�� ������.  

{% highlight cpp %}
#include <string>
#include <vector>
#include <algorithm>
using namespace std;

bool visit[10001];
bool dfs(string dept, vector<vector<string>> tickets, vector<string>& answer, int depth) {
    answer.push_back(dept);
    if (depth == tickets.size()) return true;
    // Ž�� ���̿�, Ƽ���� ���� ������ ����

    for (int i = 0; i < tickets.size(); i++) {
        if (tickets[i][0] == dept && !visit[i]) {
            visit[i] = true;
            bool is_possible = dfs(tickets[i][1], tickets, answer, depth+1);
            if (is_possible) return true;
            // ��� ��θ� ã������ ����
            visit[i] = false; 
            // �߸��� ��η� ������ Backtrack.
        }
    }

    // ������� �Դٴ� ����, �� �̻� �� ��ΰ� ���ٴ� ��!
    answer.pop_back();
    return false;
}

vector<string> solution(vector<vector<string>> tickets) {
    vector<string> answer;

    sort(tickets.begin(), tickets.end());
    dfs("ICN", tickets, answer, 0);

    return answer;
}
{% endhighlight %}
