---
layout: post
title: "���α׷��ӽ� Level 2 - ��ȭ��ȣ ���"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---

### ���α׷��ӽ� Level 2 - ��ȭ��ȣ ���.

���� ����, ������ �� �ڿ� ���� ��ȣ�� ��ġ�� ���λ簡 �ִ���
Ȯ���ϴ� ������� ������ Ǯ����.

{% highlight cpp %}
#include <string>
#include <vector>
#include <algorithm>

using namespace std;

bool solution(vector<string> phone_book) {
    bool answer = true;
    sort(phone_book.begin(), phone_book.end());
    
    for (int i = 1; i < phone_book.size(); i++) {
        if (phone_book[i - 1][0] != phone_book[i][0]) continue;
        if (phone_book[i].find(phone_book[i - 1]) != string::npos) {
            // find method�� ����ؼ�, npos�� �ƴϸ� ���λ簡 ����.
            answer = false; 
            break;
        }
    }

    return answer;
}
{% endhighlight %}

### �ٸ� Ǯ�� - Hash map

������ Ǭ �� ���ͳ��� ã�ƺ���, �ٸ� Ǯ�̰� �־���.
���� Hash map���� ����ϴ� Unorderd map�� �̿��� Ǯ�̿���.

{% highlight cpp %}
#include <string>
#include <vector>
#include <unordered_map>

using namespace std;

bool solution(vector<string> phone_book) {
    unordered_map<string, int> hash_map; // Hash map ����

    for (int i = 0; i < phone_book.size(); i++)
        hash_map[phone_book[i]] = 1;
        // ��ȭ��ȣ���� ��� Hash map�� ����.

    for (int i = 0; i < phone_book.size(); i++) {
        for (int j = 0; j < phone_book[i].size()-1; j++) {
            string number = phone_book[i].substr(0, j + 1);
            // ��ȭ��ȣ���� Substring ��� Hash map�� ���Ѵ�.
            // Hash map�� �����ϸ�, ���λ簡 ��ġ�� ���̹Ƿ� False.
            if (hash_map[number]) return false;
        }
    }

    return true;
}
{% endhighlight %}