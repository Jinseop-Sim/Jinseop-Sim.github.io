---
layout: post
title: "프로그래머스 Level 3 - 여행 경로"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [여행 경로[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/43164)

처음엔 간단한 DFS문제라고 생각하고 접근했다.  
단순히 알파벳 순서로만 뽑아서 경로를 출력하면 된다고 생각해서,  
```unordered_map```을 만든 뒤, Value로 ```priority_queue```를 넣었다.  
알파벳 빠른 순서로 다음 도착지를 찾되, 갔던 도착지는 가면 안되므로, Queue에서 Pop해준다.  

하지만 이 방법은 어떤 Case를 완전히 간과한 방법이었다.  
바로 알파벳 순서로 갔을 때, 목적지에 도달할 수 없는 경로일 수 있는 Case이다.  

__만일 가능한 경로가 2개 이상일 경우 알파벳 순서가 앞서는 경로를 return 합니다.__  
위와 같은 문구가 있었는데, 즉 가능한 경로가 없다면 순서는 상관이 없다는 것이다.  

그럼 ```visit``` 배열을 통한 Backtracking이 필요하다고 느꼈는데,  
어떻게 경로가 잘못되었을 때만 뒤로 돌아갈 지 생각이 나지 않았다.  

### 풀이 : 조건부 Backtracking

결국 다른 분들의 풀이를 참고해보니, 함수의 반환값을 ```Bool```로 두더라.  
그럼 for문을 다 돌아도 더 이상 갈 수 있는 길이 없으면, Backtrack 하는 것이다.  
그러다 깊이가 원래 들고 있던 Ticket의 수와 같아지면 True를 Return해 끝낸다.  

{% highlight cpp %}
#include <string>
#include <vector>
#include <algorithm>
using namespace std;

bool visit[10001];
bool dfs(string dept, vector<vector<string>> tickets, vector<string>& answer, int depth) {
    answer.push_back(dept);
    if (depth == tickets.size()) return true;
    // 탐색 깊이와, 티켓의 수가 같으면 종료

    for (int i = 0; i < tickets.size(); i++) {
        if (tickets[i][0] == dept && !visit[i]) {
            visit[i] = true;
            bool is_possible = dfs(tickets[i][1], tickets, answer, depth+1);
            if (is_possible) return true;
            // 모든 경로를 찾았으면 종료
            visit[i] = false; 
            // 잘못된 경로로 들어갔으면 Backtrack.
        }
    }

    // 여기까지 왔다는 것은, 더 이상 갈 경로가 없다는 것!
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
