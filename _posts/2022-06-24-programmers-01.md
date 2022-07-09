---
layout: post
title: "프로그래머스 Level 2 - 전화번호 목록"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---

- [전화번호 목록[LEVEL 2]](https://school.programmers.co.kr/learn/courses/30/lessons/42577)   

나는 먼저, 정렬을 한 뒤에 인접 번호에 겹치는 접두사가 있는지  
확인하는 방식으로 문제를 풀었다.

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
            // find method를 사용해서, npos가 아니면 접두사가 존재.
            answer = false; 
            break;
        }
    }

    return answer;
}
{% endhighlight %}

### 다른 풀이 - Hash map

문제를 푼 뒤 인터넷을 찾아보니, 다른 풀이가 있었다.  
흔히 Hash map으로 사용하는 Unorderd map을 이용한 풀이였다.

Unordered map은 일반적인 Map, Set과 다르게 정렬이 되지 않는다.  
그래서 다음과 같은 시간 복잡도를 가진다.
- Map, Set : O(log N)
- Unordered Map : O(1)

{% highlight cpp %}
#include <string>
#include <vector>
#include <unordered_map>

using namespace std;

bool solution(vector<string> phone_book) {
    unordered_map<string, int> hash_map; // Hash map 선언

    for (int i = 0; i < phone_book.size(); i++)
        hash_map[phone_book[i]] = 1;
        // 전화번호들을 모두 Hash map에 저장.

    for (int i = 0; i < phone_book.size(); i++) {
        for (int j = 0; j < phone_book[i].size()-1; j++) {
            string number = phone_book[i].substr(0, j + 1);
            // 전화번호부의 Substring 들과 Hash map을 비교한다.
            // Hash map에 존재하면, 접두사가 겹치는 것이므로 False.
            if (hash_map[number]) return false;
        }
    }

    return true;
}
{% endhighlight %}