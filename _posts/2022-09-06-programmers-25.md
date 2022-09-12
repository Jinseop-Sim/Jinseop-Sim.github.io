---
layout: post
title: "프로그래머스 Level 2 - 뉴스 클러스터링"
categories: [KAKAO] Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [뉴스 클러스터링[LEVEL 2]](https://school.programmers.co.kr/learn/courses/30/lessons/17677)  

2018 KAKAO BLIND RECRUITMENT 1차 코딩테스트 문제였다.  
문제 난이도는 그렇게 어려워보이지 않았으나, 감을 잃었는지 생각이 잘 안났다.  
적합한 자료구조를 적재적소에 쓰는 것이 필요하다!  

### Map과 Set의 동시 이용
먼저 Map의 특성은, Hash의 특성을 갖는다.  
그 말인 즉슨 단어가 몇 번 등장 했는지 셀 수 있다는 것.  
그리고 Set의 특성은, 중복을 없애준다는 것.  
이 둘을 적절하게 섞어서 이용해보면 되며, 생각해야할 점은 다음과 같다.  

- 교집합을 어떻게 구할 것인가?  
  - Map으로 단어의 수를 센 다음, 더 적은 쪽 단어의 수만 더해 나간다.
- 합집합을 어떻게 구할 것인가?
  - Map으로 단어의 수를 센 다음, 더 많은 쪽 단어의 수만 더해 나간다.
- 이 때, 두 Map에 접근을 할 Key의 모임을 Set으로 만들어 저장한다.
  - 왜? 단어 Key의 모임은 단어의 중복이 없어야 하기 때문!

{% highlight cpp %}
#include <iostream>
#include <string>
#include <unordered_map>
#include <unordered_set>
using namespace std;

unordered_map<string, int> multiset1;
unordered_map<string, int> multiset2;
unordered_set<string> store;
void init(string& str1, string& str2) {
    for (int i = 0; i < str1.size(); i++)
        str1[i] = tolower(str1[i]);
    for (int i = 0; i < str2.size(); i++)
        str2[i] = tolower(str2[i]);
}

void make_multiset(string str1, string str2) {
    for (int i = 0; i < str1.size() - 1; i++) {
        if ((str1[i] >= 'a' && str1[i] <= 'z') && (str1[i + 1] >= 'a' && str1[i + 1] <= 'z')) {
            string partial1 = str1.substr(i, 2);
            multiset1[partial1]++;
            store.insert(partial1);
        }
    }
    for (int i = 0; i < str2.size() - 1; i++) {
        if ((str2[i] >= 'a' && str2[i] <= 'z') && (str2[i + 1] >= 'a' && str2[i + 1] <= 'z')) {
            string partial2 = str2.substr(i, 2);
            multiset2[partial2]++;
            store.insert(partial2);
        }
    }
}

int calc_jaccard() {
    double inter = 0.0, uni = 0.0;

    for (auto s : store) {
        inter += min(multiset1[s], multiset2[s]);
        uni += max(multiset1[s], multiset2[s]);
    }
    
    if (uni == 0) return 65536;
    return (inter / uni) * 65536;
}

int solution(string str1, string str2) {
    int answer = 0;
    
    init(str1, str2);
    make_multiset(str1, str2);
    answer = calc_jaccard();

    return answer;
}
{% endhighlight %}