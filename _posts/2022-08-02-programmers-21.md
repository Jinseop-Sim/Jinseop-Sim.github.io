---
layout: post
title: "프로그래머스 Level 3 - 아이템 줍기"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [아이템 줍기[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/87694)

처음엔 주어진 그림만 잘 그리면 끝나는 문제라고 생각했다.  
하지만 주어진 그림을 그대로 그리면 BFS가 제대로 돌지 않음을 확인했고,  
고민 끝에 다른 사람의 풀이를 확인해보니 그림을 2배로 키우는 방법이 있었다.  

그림을 그리는 방식은 간단하다.  
1. 먼저 테두리를 따라 사각형을 모두 그린다.
2. 이후 사각형의 내부 범위만큼 모두 0으로 채워버린다.
3. 그럼 가장 바깥 테두리만 남는다!

하지만 모든 좌표를 2배로 늘릴 생각은 하지 못했다.  

### 풀이 : BFS

그림을 그렸으면, BFS를 통해서 Item이 있는 곳 까지 가면 된다.  
물론 가는 길에 계속 + 1을 해주어 거리 계산을 해주며 나아간다.  
양쪽으로 출발해 먼저 도착하는 쪽이 최단거리로 종료된다.  

{% highlight cpp %}
#include <string>
#include <vector>
#include <queue>
using namespace std;

int maps[110][110];
int dx[4] = { 1, -1, 0, 0 };
int dy[4] = { 0, 0, 1, -1 };
queue<pair<int, int>> bfs_q;
void draw_map(vector<vector<int>> rectangle) {
    for (int i = 0; i < rectangle.size(); i++)
        for (int j = 0; j < rectangle[0].size(); j++)
            rectangle[i][j] *= 2; 
            // 도대체 이런 생각은 어떻게 하나?
        
    for (int outer = 0; outer < rectangle.size(); outer++) {
        for (int i = rectangle[outer][0]; i <= rectangle[outer][2]; i++) {
            maps[rectangle[outer][1]][i] = 1;
        }
        for (int i = rectangle[outer][1]; i <= rectangle[outer][3]; i++) {
            maps[i][rectangle[outer][0]] = 1;
        }
        for (int i = rectangle[outer][2]; i >= rectangle[outer][0]; i--) {
            maps[rectangle[outer][3]][i] = 1;
        }
        for (int i = rectangle[outer][3]; i >= rectangle[outer][1]; i--) {
            maps[i][rectangle[outer][2]] = 1;
        }
    }
    // 테두리 그리기

    for (int outer = 0; outer < rectangle.size(); outer++) {
        for (int i = rectangle[outer][1] + 1; i < rectangle[outer][3]; i++)
            for (int j = rectangle[outer][0] + 1; j < rectangle[outer][2]; j++)
                maps[i][j] = 0;
    }
    // 내부 비우기
}

void bfs(int st_x, int st_y, int itemX, int itemY) {
    bfs_q.push({ st_y, st_x });

    while (!bfs_q.empty()) {
        int y = bfs_q.front().first;
        int x = bfs_q.front().second;
        bfs_q.pop();

        if (x == itemX && y == itemY) break;

        for (int i = 0; i < 4; i++) {
            int nx = x + dx[i];
            int ny = y + dy[i];
            if (maps[ny][nx] == 1) {
                bfs_q.push({ ny, nx });
                maps[ny][nx] = maps[y][x] + 1;
            }
        }
    }
}

int solution(vector<vector<int>> rectangle, int characterX, int characterY, int itemX, int itemY) {
    int answer = 0;
    characterX *= 2; characterY *= 2; itemX *= 2; itemY *= 2; 
    // 이 생각을 어떻게 할까?

    draw_map(rectangle);
    bfs(characterX, characterY, itemX, itemY);

    answer = maps[itemY][itemX] / 2;
    return answer;
}
{% endhighlight %}