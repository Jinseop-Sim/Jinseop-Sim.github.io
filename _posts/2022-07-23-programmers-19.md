---
layout: post
title: "프로그래머스 Level 3 - 경주로 건설"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
math: true
---

- [경주로 건설[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/67259)

2020 KAKAO 인턴십 문제였다.  

처음엔 문제만 보고 단순히 Backtracking을 이용하면 될 것이라 생각했다.  
내가 고려했던 것은 아래와 같다.  

1. Backtracking으로 경로를 모두 탐색한다.
2. 코너를 도는 지 안도는 지는 이전 탐색에서 넘어온 매개변수로 판단.
3. 이 때 까지 계산한 값보다 커지면 Return 함으로써 Cut.  

### 1차 시도 : Backtracking(DFS)

예상은 했지만, $$1/3$$의 Case가 시간초과가 발생했다.
내 Code를 확인을 해보니, 계산된 min_cost의 값이 너무 늦게 작아지는게 문제였다.  

{% highlight cpp %}
#include <string>
#include <vector>
using namespace std;
#define BIGNUM 10000000
#define STRAIGHT 100
#define CORNER 600

int dx[4] = { 0, 0, 1, -1 };
int dy[4] = { 1, -1, 0, 0 };
int min_cost = BIGNUM , cost = 0;

void backtrack(int direction, int st_x, int st_y, int cost, vector<vector<int>> board, vector<vector<bool>>& visit) {
    if ((st_x == board.size() - 1) && (st_y == board.size() - 1)) {
        if (min_cost > cost) min_cost = cost;
        return;
    }

    if (min_cost < cost) return;

    for (int i = 0; i < 4; i++) {
        int nx = st_x + dx[i];
        int ny = st_y + dy[i];
        if ((nx >= 0 && nx < board.size()) && (ny >= 0 && ny < board.size())) {
            if (!visit[nx][ny] && board[nx][ny] == 0) {
                visit[nx][ny] = true;
                if (direction != i + 1 && direction != 9) {
                    backtrack(i + 1, nx, ny, cost + CORNER, board, visit);
                    visit[nx][ny] = false;
                }
                else {
                    backtrack(i + 1, nx, ny, cost + STRAIGHT, board, visit);
                    visit[nx][ny] = false;
                }
            }
        }
    }
}

int solution(vector<vector<int>> board) {
    int answer = 0;
    vector<vector<bool>> visit(board.size(), vector<bool>(board.size(), 0));

    visit[0][0] = true;
    backtrack(9, 0, 0, 0, board, visit);

    answer = min_cost;
    return answer;
}
{% endhighlight %}

### 2차 시도 : Backtracking + DP

결국 구글의 힘을 빌렸다.  
다른 사람들의 풀이를 보니, DP와 DFS를 합쳐서 풀었음을 알 수 있었다.  

DP가 사용이 가능한 이유는,  
내가 마지막 도착점에서 비용이 최소가 되려면,  
어떤 경로든 ```(x, y)``` 까지의 비용이 최소로 유지가 되어야 하기 때문이다.  

그리고 또, DFS를 이용하기 때문에 탐색하는 4방향의 순서도 문제가 되었다.  
```우 상 좌 하```의 순서대로 탐색을 하니 통과가 가능했다.  

{% highlight cpp %}
#include <string>
#include <vector>
using namespace std;
#define INF 10000000
#define STRAIGHT 100
#define CORNER 600

int dx[4] = { 0, 1, 0, -1 };
int dy[4] = { 1, 0, -1, 0 };
int DP[26][26];
int min_cost = INF;

void init_DP(int n) {
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            DP[i][j] = INF;
            // DP 배열을 무한대로 초기화.
        }
    }
}

void backtrack(int direction, int st_x, int st_y, int cost, vector<vector<int>> board, vector<vector<bool>>& visit) {
    if (DP[st_x][st_y] != INF && DP[st_x][st_y] < cost) return;
    // 만약 Backtrack에 의해 (x, y)로 돌아왔으나, cost가 더 크다면?  
    // 검사를 할 필요가 없으니, Return.

    if (min_cost < cost) return;
    // 검사를 하는 도중 cost가 지금까지 구해놓은 답보다 커지면 Return.

    DP[st_x][st_y] = min(DP[st_x][st_y], cost);
    // 현재 (x, y)까지의 든 비용을 배열에 저장.
    // Backtracking을 하며 점점 작아진다.

    if ((st_x == board.size() - 1) && (st_y == board.size() - 1)) {
        min_cost = min(min_cost, cost);
        // 도착하면, 더 작은 값을 답에 저장.
        return;
    }

    for (int i = 0; i < 4; i++) {
        int nx = st_x + dx[i];
        int ny = st_y + dy[i];
        if ((nx >= 0 && nx < board.size()) && (ny >= 0 && ny < board.size())) {
            if (!visit[nx][ny] && board[nx][ny] == 0) {
                visit[nx][ny] = true;
                if (direction != i + 1 && direction != 9) {
                    // 방향 판단의 기준이 된다.
                    // 이전 분기의 방향과 다르면 코너인 것!
                    backtrack(i + 1, nx, ny, cost + CORNER, board, visit);
                    visit[nx][ny] = false;
                }
                else {
                    backtrack(i + 1, nx, ny, cost + STRAIGHT, board, visit);
                    visit[nx][ny] = false;
                }
            }
        }
    }
}

int solution(vector<vector<int>> board) {
    int answer = 0;
    vector<vector<bool>> visit(board.size(), vector<bool>(board.size(), 0));

    init_DP(board.size());
    visit[0][0] = true;
    backtrack(9, 0, 0, 0, board, visit);
    // 9는 출발점을 의미한다.

    answer = min_cost;
    return answer;
}
{% endhighlight cpp %}