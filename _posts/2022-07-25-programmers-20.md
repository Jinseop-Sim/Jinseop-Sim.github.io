---
layout: post
title: "프로그래머스 Level 3 - 보행자 천국"
categories: KAKAO Programmers
tags: [cpp]
author:
  - Jinseop Sim
---

- [보행자 천국[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/1832)

2017 KAKAO Code 예선 문제였다.  
저번에 풀었던 Level 3 문제인 경주로 건설 문제와 똑같은 문제라고 생각했다.  
그러나 생각대로 풀리진 않았다.  

내가 생각한 풀이 방식은 아래와 같았다.  
1. DP를 이용하기 위해 먼저 MAP을 초기화 하되, 1이 나오면 멈춘다.
2. 조건에 따라 위의 칸이 2면 그 아래 칸에는 더하지 않고, 뒷 칸만 더한다.  
3. 1이면 당연히 더하지 않고, 1이 아닌 칸만 더한다.  
4. 마지막 칸에 경로의 수가 담긴다.  

### 1차 시도 : Backtracking
하지만 이 풀이대로 잘 풀리지 않아서, Backtracking으로 접근했다.  
그러나 틀린 풀이 방법이었다.  

현재 칸이 2라면, 이전에 온 방향과 같은 방향으로만 진행하도록 한 코드인데,  
분명히 무언가 칸을 도는 순서에서 문제가 있는 것 같다.  

{% highlight cpp %}
#include <iostream>
#include <vector>
using namespace std;

int MOD = 20170805, cnt = 0;
int dx[4] = { 0, 1 };
int dy[4] = { 1, 0 };
void backtrack(int x, int y, int past, vector<vector<int>> city_map, vector<vector<bool>>& visit) {
    if ((x == city_map.size() - 1) && (y == city_map[0].size() - 1)) {
        cnt++;
        return;
    }

    for (int i = 0; i < 2; i++) {
        if (city_map[x][y] == 2)
            if (i + 1 != past && past != 9) continue;

        int nx = x + dx[i];
        int ny = y + dy[i];
        if ((nx >= 0 && nx < city_map.size()) && (ny >= 0 && ny < city_map[0].size())) {
            if (!visit[nx][ny] && city_map[nx][ny] != 1) {
                visit[nx][ny] = true;
                backtrack(nx, ny, i + 1, city_map, visit);
                visit[nx][ny] = false;
            }
        }
    }
}

int solution(int m, int n, vector<vector<int>> city_map) {
    int answer = 0;

    vector<vector<bool>> visit(m, (vector<bool>(n, 0)));
    backtrack(0, 0, 9, city_map, visit);

    answer = cnt % 20170805;
    return answer;
}
{% endhighlight %}

### 2차 시도 : 돌고 돌아 DP
결국 이 문제는 DP 문제였다.  
내가 과연 방향을 어떻게 저장해나갈지 고민을 했었는데,  
나는 MAP을 2개를 만들어놓고 계속 비교해 나가며 채우려고 했다.  
하지만 방향을 저장할 수는 없었다.  

결국 다른 사람들의 풀이를 보고 얻은 방법은, ```Pair<위, 아래>```의 Vector를 만드는 것이었다.  

{% highlight cpp %}
#include <iostream>
#include <vector>
using namespace std;

int MOD = 20170805;
void init_map(int m, int n, vector<vector<int>> city_map, vector<vector<pair<int, int>>>& DP) {
    for (int i = 1; i < m; i++) {
        if (city_map[i][0] == 1) break;
        // 1(통행금지) 가 나오면 멈춤!
        DP[i][0].first = 1;
    }
    // 아래로 초기화
    for (int i = 1; i < n; i++) {
        if (city_map[0][i] == 1) break;
        DP[0][i].second = 1;
    }
    // 오른쪽으로 초기화
}
int solution(int m, int n, vector<vector<int>> city_map) {
    int answer = 0;

    vector<vector<pair<int, int>>> DP(m, vector<pair<int, int>>(n, { 0, 0 }));
    init_map(m, n, city_map, DP);

    for (int i = 1; i < m; i++) {
        for (int j = 1; j < n; j++) {
            if (city_map[i - 1][j] == 0)
                DP[i][j].first = (DP[i - 1][j].first + DP[i - 1][j].second) % MOD;
            if (city_map[i - 1][j] == 1)
                DP[i][j].first = 0; // 내 위의 수가 1이면, 나한테 올 수 없다. 0!
            if (city_map[i - 1][j] == 2)
                DP[i][j].first = DP[i - 1][j].first;
                // 내 위의 수가 2이면, 나한테 아래로만 올 수 있다.

            if (city_map[i][j - 1] == 0)
                DP[i][j].second = (DP[i][j - 1].first + DP[i][j - 1].second) % MOD;
            if (city_map[i][j - 1] == 1)
                DP[i][j].second = 0; // 내 앞의 수가 1이면, 나한테 올 수 없다. 0!
            if (city_map[i][j - 1] == 2)
                DP[i][j].second = DP[i][j - 1].second;
                // 내 앞의 수가 2이면, 나한테 앞으로만 올 수 있다.
        }
    }

    if (m == 1 && n == 1) answer = 1;
    else answer = (DP[m - 1][n - 1].first + DP[m - 1][n - 1].second) % MOD;
    // 마지막에 위 아래의 경우를 모두 더해준다.

    return answer;
}
{% endhighlight %}