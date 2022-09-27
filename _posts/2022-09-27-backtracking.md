---
layout: post
title: "Graph : Backtracking"
categories: Algorithm
tags: [cpp]
author:
  - Jinseop Sim
---
보통 그래프 문제를 푸는 방식은 DFS와 BFS로 나뉘게 된다.  
하지만 그 둘만으로는 풀리지 않는 문제가 있다. 그럴 떄에는 일종의 기술이 필요하다.  

만약 내가 지도를 들고 정글을 헤쳐나가고 있다고 생각해보자.  
지도 상으로는 지금 가는대로 간다면 막다른 길에 도달하게 된다.  
그래도 나는 앞으로 가서 막다른 길임을 두 눈으로 확인해야 하는가?  

상식적으로 생각해보면, 굳이 막다른 길임을 알았을 때는 다른 길로 다시 돌아가면 된다.  
이것을 알고리즘에서는 __Pruning(가지치기)__ 기법이라고 말한다.  
우리가 흔히 사용하던 DFS에 이 가지치기를 더한 것이 바로 Backtracking(되추적)이다!  

DFS로 깊이 우선 탐색을 진행하며 모든 경우를 다 뒤지는 것이 바보같은 행동인 경우가 있다.  

대표적인 문제로는 체스판에 퀸을 놓을 수 있는 가짓수를 세는 __N-Queens Problem__ 이 있으며,  
조합이나 순열을 구현할 때에도 Backtracking이 이용된다.  

### Example : N-Queens Problem
한 행씩 내려가며 Queen을 놓아야 하는데, 생각할 조건은 아래와 같다.  

- Queen을 기준으로 상, 하, 좌, 우, 대각선은 다른 Queen을 놓을 수 없다.
    - 이 조건에 부합하지 않는 경우는 Pruning(가지치기) 한다.

하나의 조건만 생각하면 되는 문제이지만, 저 조건을 어떻게 구현하는지가 개인의 역량에 달렸다.  
나는 1차원 배열에 행과 열을 동시에 표현해 비교하는 방식으로 구현했다.  

{% highlight cpp %}
#include <string>
#include <vector>
using namespace std;

int result = 0;
bool validate(vector<int> check, int row, int col) {
    int curr_depth = row + 1;

    if (check[col] == 0) {
        // 해당 열에 세로로 퀸이 하나도 없는 경우.
        for (int i = 0; i < check.size(); i++) {
            if (i == col) continue;
            if (check[i] != 0) {
                if ((curr_depth - check[i]) == abs(i - col))
                // 이 부분은 자신의 대각선 위를 비교하는 경우.
                // (x, y) ==> (x - n, y - n) 이 대각선 위, 아래인 원리를 이용
                    return false;
            }
        }
        
        return true;
    }
    else return false;
}

void backtrack(int depth, int size, vector<bool>& visit, vector<int>& check) {
    if (depth == size) {
        result++;
        return;
    }

    for (int i = 0; i < size; i++) {
        if (!visit[depth] && validate(check, depth, i)) {
            visit[depth] = true;
            check[i] = depth + 1;
            backtrack(depth + 1, size, visit, check);
            // Backtracking 구현.
            visit[depth] = false;
            check[i] = 0;
        }
    }
}

int solution(int n) {
    int answer = 0;
    vector<int> check(n, 0);
    vector<bool> visit(n, 0);

    backtrack(0, n, visit, check);
    answer = result;
    return answer;
}
{% endhighlight %}