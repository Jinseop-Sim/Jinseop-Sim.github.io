---
layout: post
title: "프로그래머스 Level 3 - 숫자 게임"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [숫자 게임[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/12987)  

### Backtracking
처음에는 신박한 방법이 생각나지 않아, 완전 탐색으로 접근했다.  
당연히 시간초과가 날 줄은 알았지만 딱히 방법이 생각나지 않아 구현만 해보았다.  

이렇게 구현 해보는 것도 다 나중을 위한 거름이 될 거라고 생각한다.  

### 단순 탐색
이후엔 단순하게 2중 포문을 통해서 탐색해보았다.  
B배열에서 A배열보다 큰 수가 등장하면 바로 ```break```를 걸기 때문에,  
위의 완전 탐색보다는 시간이 훨씬 빨라졌다.  

하지만 그래도 효율성에서는 통과하지를 못했다.  

### Priority Queue
곰곰히 생각을 해보았다.  
문득 생각해보니 정렬을 한 뒤에 검사를 해도 상관이 없지 않을까?  

예시 Case와 같이 ```A{5,1,3,7}, B{2,2,6,8}```이 있다고 가정해보자.  
A의 순서는 ```5,1,3,7```로 고정이 되어있지만, 사실 순서를 바꿔도 상관없다.  

__왜일까?__  
A를 ```1,3,5,7```로 오름차순 정렬한 뒤, B를 ```2,2,6,8```로 놓고 생각해보면,  
사실 A가 ```5,1,3,7``` 일 때, B가 ```6,2,2,8```로 된 것과 똑같다!  
이 점을 이용해서, ```Priority Queue```를 이용해 해결한다.  

{% highlight cpp %}
#include <string>
#include <vector>
#include <queue>
using namespace std;

priority_queue<int, vector<int>, greater<>> A_q;
priority_queue<int, vector<int>, greater<>> B_q;
void fill_queue(vector<int> A, vector<int> B) {
    for (int i = 0; i < A.size(); i++) {
        A_q.push(A[i]);
        B_q.push(B[i]);
    }
}

int solution(vector<int> A, vector<int> B) {
    int answer = 0;

    fill_queue(A, B);
    while (!B_q.empty()) {
        if (A_q.top() < B_q.top()) {
            A_q.pop();
            B_q.pop();
            answer++;
            continue;
        }

        B_q.pop();
    }

    return answer;
}
{% endhighlight %}