---
layout: post
title: "프로그래머스 Level 3 - 고고학 최고의 발견"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [고고학 최고의 발견[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/131702)

이번 문제는 프로그래머스의 레벨 3 고고학 문제이다.  
문제를 한 번 차근차근 읽어보자.  

최대 ```8x8``` 크기의 암호판이 주어진다.  
암호판의 화살표를 시계 방향으로 돌려 모두 12시로 맞추어야 한다.  
암호판 하나를 돌리면 동서남북 네 방향의 화살표가 모두 돌아간다.  
암호를 풀 수 있는 최소 횟수를 구하라!  

어떤 식으로 문제를 풀어나가야 할까?  

### 완전 탐색?
가장 먼저 든 생각은 구현 + 완전탐색으로 풀면 되지 않을까? 이다.  
가운데 판 및 동서남북 방향의 판을 돌리는 것은 손쉽게 구현할 수 있다.  
하지만 문제는 어떤식으로 모든 판을 돌려볼 것인가이다.  

만약 이걸 백트래킹 방식으로 구현한다면 어떨까?  
백트래킹 방식을 적용하려면, 재귀에 대한 중지 조건이 반드시 필요하다.  
하지만 이 문제는 판을 최대로 돌릴 수 있는 횟수 같은 것이 정해져 있지 않다.  
즉 백트래킹을 중지할 수 있는 기준이 없으므로 사용할 수 없을 것 같다.  

```8x8``` 크기의 보드에서 고를 수 있는 칸은 총 64칸이다.  
이 때 1칸 ~ 64칸을 돌리는 경우까지 모두 직접 계산을 하면 얼마나 걸릴까?  
64칸에 대해서 우리는 한 칸당 4개의 회전을 선택할 수 있다. 즉, ```4^64```가 된다.  
이는 터무니 없이 큰 수 이므로, 완전 탐색으로는 풀 수 없을 것 같다.  

### Greedy?
그리디로 풀 수 있지 않을까 잠시 생각해보았다.  
그리디하게 풀기에는 너무 많은 경우의 수가 존재하는 문제이다.  
단순히 12시로 돌아간 화살표가 가장 적은 칸을 택한다거나 하는  
간단한 규칙으로 풀 수 있는 문제는 아닌 것 같다.  

### 제한된 완전탐색
결국 다른 사람들의 풀이를 참고하게 되었다.  
풀이 방식은 완전탐색을 제한된 조건 내에서 진행하는 것이었다.  
다시 말해, 아이디어가 굉장히 중요한 문제였다.  

위에서 말했듯이 전체를 다 돌리려면 ```4^64```개의 경우의 수가 나온다.  
하지만 곰곰히 생각해보면 이 문제는 시간 단축을 위한 두 단서를 제공하고 있다.  

1. 회전은 4번을 초과하면 의미가 없다.
2. 모든 칸은 첫번째 줄의 상태에 종속된다.

시간 단축의 핵심은 2번 단서이다.  
첫번째 줄이 결정이 되면, 첫번째 줄의 칸을 돌리기 위해 두번째 줄이 돌아간다.  
그럼 두번째 줄이 결정되므로, 그 아래의 줄도 돌아가게 될 것이다.  

즉, 우리는 첫번째 줄만 결정해주면 그 아래는 자동으로 맞춰져야 한다!  
왜? 아랫줄의 칸을 돌리면 윗줄의 칸이 같이 돌아가기 때문에!  
그럼 첫번째 줄의 모든 경우를 생각하는 경우의 수는? ```4^8```이 된다.  
이는 ```65563```밖에 되지 않는 수로, 충분히 해볼만한 탐색이다.  

나는 백트래킹을 통해 첫째줄의 모든 경우의 수를 만들어 주었고,  
첫째줄의 회전이 끝났을 때 그 아래로 모든 줄의 회전 횟수를 세주었다.  

최종적으로 구현은 아래와 같이 되었다.  
{% highlight cpp %}
void turn_clockwise(int tar_x, int tar_y, vector<vector<int>> &tar_map, int row, int num) {
    for (int i = 0; i < 5; i++) {
        int nx = tar_x + dx[i];
        int ny = tar_y + dy[i];

        if ((nx >= 0 && nx < row) && (ny >= 0 && ny < row))
            tar_map[nx][ny] = (tar_map[nx][ny] + num) % 4;
    }
}

bool check_unlock(int row, vector<vector<int>>& tar_map) {
    for (int i = 0; i < row; i++)
        if (tar_map[row - 1][i] != 0)
            return false;

    return true;
}

void dfs(vector<vector<int>> &tar_map, int depth) {
    if (depth == tar_map.size()) {
        int partial_sum = 0;
        // 이 부분에서 첫 줄 회전
        vector<vector<int>> copied_map = tar_map;
        for (int i = 0; i < rot_vec.size(); i++)
            turn_clockwise(0, i, copied_map, copied_map.size(), rot_vec[i]);

        // 여기부터 첫 줄 제외 다른 row들 돌려보기
        for (int i = 1; i < copied_map.size(); i++) {
            for (int j = 0; j < copied_map.size(); j++) {
                int prev_lock = copied_map[i - 1][j];
                if (!prev_lock)
                    continue;

                int rot_num = 4 - prev_lock;
                turn_clockwise(i, j, copied_map, copied_map.size(), rot_num);
                partial_sum += rot_num;
            }
        }

        if (check_unlock(copied_map.size(), copied_map))
            min_rot_num = min(min_rot_num, sum + partial_sum);

        return;
    }

    for (int i = 0; i < 4; i++) {
    // 우리는 0 1 2 3 중에서 값을 고르도록 한다.
        sum += i;
        rot_vec.push_back(i); // 회전 동작, 미리 돌리면 안됨
        dfs(tar_map, depth + 1);
        sum -= i;
        rot_vec.pop_back();
    }
}
{% endhighlight %}  

아이디어를 떠올리기가 굉장히 어려운 문제였다.  
이런 문제들이 요즘 코딩테스트에는 많이 등장하는 것 같다.  
좀 더 집중하고 열심히 해보자.  
