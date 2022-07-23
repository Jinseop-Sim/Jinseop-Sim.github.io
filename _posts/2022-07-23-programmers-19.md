---
layout: post
title: "���α׷��ӽ� Level 3 - ���ַ� �Ǽ�"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
math: true
---

- [���ַ� �Ǽ�[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/67259)

2020 KAKAO ���Ͻ� ��������.  

ó���� ������ ���� �ܼ��� Backtracking�� �̿��ϸ� �� ���̶� �����ߴ�.  
���� ����ߴ� ���� �Ʒ��� ����.  

1. Backtracking���� ��θ� ��� Ž���Ѵ�.
2. �ڳʸ� ���� �� �ȵ��� ���� ���� Ž������ �Ѿ�� �Ű������� �Ǵ�.
3. �� �� ���� ����� ������ Ŀ���� Return �����ν� Cut.  

### 1�� �õ� : Backtracking(DFS)

������ ������, $$1/3$$�� Case�� �ð��ʰ��� �߻��ߴ�.
�� Code�� Ȯ���� �غ���, ���� min_cost�� ���� �ʹ� �ʰ� �۾����°� ��������.  

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

### 2�� �õ� : Backtracking + DP

�ᱹ ������ ���� ���ȴ�.  
�ٸ� ������� Ǯ�̸� ����, DP�� DFS�� ���ļ� Ǯ������ �� �� �־���.  

DP�� ����� ������ ������,  
���� ������ ���������� ����� �ּҰ� �Ƿ���,  
� ��ε� ```(x, y)``` ������ ����� �ּҷ� ������ �Ǿ�� �ϱ� �����̴�.  

�׸��� ��, DFS�� �̿��ϱ� ������ Ž���ϴ� 4������ ������ ������ �Ǿ���.  
```�� �� �� ��```�� ������� Ž���� �ϴ� ����� �����ߴ�.  

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
            // DP �迭�� ���Ѵ�� �ʱ�ȭ.
        }
    }
}

void backtrack(int direction, int st_x, int st_y, int cost, vector<vector<int>> board, vector<vector<bool>>& visit) {
    if (DP[st_x][st_y] != INF && DP[st_x][st_y] < cost) return;
    // ���� Backtrack�� ���� (x, y)�� ���ƿ�����, cost�� �� ũ�ٸ�?  
    // �˻縦 �� �ʿ䰡 ������, Return.

    if (min_cost < cost) return;
    // �˻縦 �ϴ� ���� cost�� ���ݱ��� ���س��� �亸�� Ŀ���� Return.

    DP[st_x][st_y] = min(DP[st_x][st_y], cost);
    // ���� (x, y)������ �� ����� �迭�� ����.
    // Backtracking�� �ϸ� ���� �۾�����.

    if ((st_x == board.size() - 1) && (st_y == board.size() - 1)) {
        min_cost = min(min_cost, cost);
        // �����ϸ�, �� ���� ���� �信 ����.
        return;
    }

    for (int i = 0; i < 4; i++) {
        int nx = st_x + dx[i];
        int ny = st_y + dy[i];
        if ((nx >= 0 && nx < board.size()) && (ny >= 0 && ny < board.size())) {
            if (!visit[nx][ny] && board[nx][ny] == 0) {
                visit[nx][ny] = true;
                if (direction != i + 1 && direction != 9) {
                    // ���� �Ǵ��� ������ �ȴ�.
                    // ���� �б��� ����� �ٸ��� �ڳ��� ��!
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
    // 9�� ������� �ǹ��Ѵ�.

    answer = min_cost;
    return answer;
}
{% endhighlight cpp %}