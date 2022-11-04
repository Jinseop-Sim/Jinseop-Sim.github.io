---
layout: post
title: "프로그래머스 Level 3 - 단속 카메라"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [단속 카메라[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/42884)  

처음 봤을 때는 직접 동작을 구현하려고 했다.  
내가 생각했던 문제의 핵심은 아래와 같다.  
- 겹치는 부분이 가장 많은 지점부터 설치를 한다.

그럼 직접 동작을 구현하려면, 겹치는 부분이 가장 많은 지점을 먼저 알아야 한다.  
또한 특정 구간들에 설치를 했으면 설치된 구간은 다음 탐색에서 제외되어야 한다.  
아무리 생각해봐도 이런 식의 완전 탐색은 너무 효율이 떨어진다고 생각했다.  

생각을 곰곰히 해본 결과, 다른 사람들의 풀이를 참고하기로 했다.  
원리는 생각보다 간단했으며 내가 생각한 것에 조금 더 추가가 되었다.  
문제에서 요구되는 논리는 아래와 같다.  

- 겹치는 부분이 가장 많은 지점부터 설치를 하는 것은 맞다.
  - 그럼 효율적으로 겹치는 부분을 찾으려면 어떻게 해야하는가?
- 진입 지점이 빠른 순 / 진출 지점이 빠른 순으로 정렬을 하자!
  - 그렇다면 앞에서 부터 훑으며 겹치는 지점들을 쉽게 셀 수가 있다.
  - Peak Time 문제 등에 사용되는 Sweep Algorithm과 유사하다.

### Greedy
{% highlight cpp %}
#include <string>
#include <vector>
#include <algorithm>
using namespace std;

int solution(vector<vector<int>> routes) {
    int answer = 1;

    sort(routes.begin(), routes.end());
    int curr_camera = routes[0][1];

    for (int i = 1; i < routes.size(); i++) {
        if (curr_camera < routes[i][0]) {
            answer++;
            curr_camera = routes[i][1];
        }
        if (routes[i][1] <= curr_camera)
            curr_camera = routes[i][1];
    }

    return answer;
}
{% endhighlight %}