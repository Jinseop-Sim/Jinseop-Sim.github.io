---
layout: post
title: "프로그래머스 Level 3 - 순위"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---

### 프로그래머스 Level 3 : 순위

도저히 풀 방법이 생각나지 않아서, 인터넷에 찾아보니  
Floyd Warshall 알고리즘을 이용해서 푸는 문제였다.  

(내용 추가 예정.)

{% highlight cpp %}
#include <string>
#include <vector>
using namespace std;

int solution(int n, vector<vector<int>> results) {
    int answer = 0;
    int info[101][101] = {0, };
    
    for(int i = 0; i<results.size(); i++) info[results[i][0]][results[i][1]] = 1;
    
    for(int i = 1; i<=n; i++){ // Floyid Warshall 알고리즘 구현
        for(int j = 1; j<=n; j++){
            for(int k =1; k<=n; k++){
                if(info[j][i] == 1 && info[i][k]) info[j][k] = 1;
            }
        }
    }
    
    for(int i = 1; i<=n; i++){
        int can_calc = 0;
        for(int j = 1; j<=n; j++) can_calc += (info[j][i] + info[i][j]);
        if(can_calc == n-1) answer++;
    }
    
    return answer;
}
{% endhighlight %}