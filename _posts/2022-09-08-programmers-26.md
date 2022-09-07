---
layout: post
title: "프로그래머스 Level 3 - 공 이동 시뮬레이션"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [공 이동 시뮬레이션[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/17677)  

월간 코드 챌린지의 레벨 3 문제였다.  
처음엔 무작정 Brute Force로 풀어 보았다.  
하지만 이 문제에선 범위가 10억까지 가기 때문에 당연히 시간초과가 났다.  

### 1차 시도 : Brute force
{% highlight cpp %}
#include <string>
#include <vector>
#include <algorithm>
using namespace std;

bool move_ball(int st_x, int st_y, int dest_x, int dest_y, vector<vector<int>> queries, vector<vector<char>> maps) {
    int col_limit = maps[0].size();
    int row_limit = maps.size();

    maps[st_x][st_y] = 'P'; // init

    int new_x = st_x, new_y = st_y;
    int prev_x = st_x, prev_y = st_y;
    for (auto query : queries) {
        switch (query[0])
        {
        case 0:
            if (prev_y - query[1] >= 0) {
                new_x = prev_x;
                new_y = prev_y - query[1];
                swap(maps[prev_x][prev_y], maps[new_x][new_y]);
            }
            break;
        case 1:
            if (prev_y + query[1] < col_limit) {
                new_x = prev_x;
                new_y = prev_y + query[1];
                swap(maps[prev_x][prev_y], maps[new_x][new_y]);
            }
            break;
        case 2:
            if (prev_x - query[1] >= 0) {
                new_x = prev_x - query[1];
                new_y = prev_y;
                swap(maps[prev_x][prev_y], maps[new_x][new_y]);
            }
            break;
        case 3:
            if (prev_x + query[1] < row_limit) {
                new_x = prev_x + query[1];
                new_y = prev_y;
                swap(maps[prev_x][prev_y], maps[new_x][new_y]);
            }
            break;
        }
        prev_x = new_x; prev_y = new_y;
    }

    if (maps[dest_x][dest_y] == 'P') return true;
    else return false;
}

long long do_query(int n, int m, int x, int y, vector<vector<int>> queries, vector<vector<char>> maps) {
    long long answer = 0;

    for (int i = 0; i < n; i++)
        for (int j = 0; j < m; j++)
            if (move_ball(i, j, x, y, queries, maps)) answer++;

    return answer;
}

long long solution(int n, int m, int x, int y, vector<vector<int>> queries) {
    long long answer = 0;

    vector<vector<char>> maps(n, vector<char>(m, '0'));
    answer = do_query(n, m, x, y, queries, maps);

    return answer;
}
{% endhighlight %}

곰곰히 생각을 해보다가, 도저히 내 머리로는 푸는데에 한계가 있었다.  
그래서 다른 사람들의 풀이를 결국 보게 되었다.  