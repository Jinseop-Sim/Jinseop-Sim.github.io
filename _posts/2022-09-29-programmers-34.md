---
layout: post
title: "���α׷��ӽ� Level 3 - �ְ��� ����"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
  math: true
---
- [�ְ��� ����[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/12938)  

### 1�� �õ� : Backtracking
ó���� n���� ���� ���� s�� �Ǵ� ������ ��� ���ؼ� ���� �����״�.  
������ ����ߴ����, �翬�ϰԵ� �ð��ʰ��� �߻��ߴ�.  

### 2�� �õ� : ������ ����
������ ������ ���� �ʾƼ� �ٸ� �е��� Ǯ�̸� �����ߴ�.  
������ ���̰� ���� Ǯ�̿���. �ٽ��� �Ʒ��� ����.  

- ���� �ִ밡 �Ǵ� ���� �ִ��� �յ��ϰ� ���� �й�Ǿ��� ���̴�.  

�� ������ �������� �����غ���.  
$$a + b = C$$(C�� ���)��� �����غ���.  
$$a + (C - a) = C$$ ��� �ֵ� ���� ���̴�.  
�׷� �ᱹ �츮�� ���ϴ� ���� $$(a(C - a)) = -a^2 - Ca$$ ��� ���� �Լ��� �ȴ�.  
�ش� �����Լ��� �Ʒ� �׸��� ���� �������� �� �� �ִ�.  

����, �ִ밡 �Ǵ� a���� C/2�� �ȴ�.  
������ C�� Ȧ���� ��쿣 C/2�� �Ǽ��̹Ƿ�, ```{C-1, C}```�� �� �� �ִ밡 �� ���̴�.  

���� ������ ���� �ڵ带 ¥�� �Ʒ��� ����.  
{% highlight cpp %}
#include <string>
#include <vector>

using namespace std;

vector<int> solution(int n, int s)
{
    vector<int> answer;

    if (n > s) {
        answer.push_back(-1);
        return answer;
    }

    for(int i = 1; i <= n; i++)
        answer.push_back(s / n);
    
    for(int i = 1; i <= s % n; i++)
        answer[n - i]++;

    return answer;
}
{% endhighlight %}