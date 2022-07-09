---
layout: post
title: "프로그래머스 Level 3 - 순위"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---

- [순위[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/49191)   

node간의 승자, 패자 결과가 주어졌을 때  
순위를 매길 수 있는 node의 수를 출력하는 문제였다.  

도저히 풀 방법이 생각나지 않아서, 인터넷에 찾아보니  
Floyd Warshall 알고리즘을 이용해서 푸는 문제였다.  

Floyd Warshall 알고리즘과 인접 행렬을 이용한다.  
모든 선수에 대해서 경로를 확보해야 하므로 Floyd Warshall 알고리즘을 사용한다.

{% highlight cpp %}
#include <string>
#include <vector>
using namespace std;

int solution(int n, vector<vector<int>> results) {
    int answer = 0;
    int info[101][101] = {0, };
    
    for(int i = 0; i<results.size(); i++) info[results[i][0]][results[i][1]] = 1;
    // 받은 결과를 인접 행렬에 저장한다.
    // 인접 행렬의 [x][y]는 x가 y를 이겼다는 뜻! 

    for(int i = 1; i<=n; i++){ // Floyid Warshall 알고리즘 구현
        for(int j = 1; j<=n; j++){
            for(int k =1; k<=n; k++){
                if(info[j][i] && info[i][k]) info[j][k] = 1;
                // 1번이 2번을 이기고, 2번이 4번을 이겼으면,
                // 1번은 반드시 4번을 이기는 것이 되는 알고리즘이다.
            }
        }
    }
    
    for(int i = 1; i<=n; i++){
        int can_calc = 0;
        for(int j = 1; j<=n; j++) can_calc += (info[j][i] + info[i][j]);
        // 순위를 매길 수 있는 짝을 모두 세었을 때, n-1 개가 나오면
        // 해당 선수의 순위는 매길 수 있는 것이다.
        if(can_calc == n-1) answer++;
    }
    
    return answer;
}
{% endhighlight %}