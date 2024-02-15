---
layout: post
title: "프로그래머스 Level 3 - 리틀 프렌즈 사천성"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [리틀 프렌즈 사천성[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/1836)

이번 문제는 프로그래머스의 레벨 3 리틀 프렌즈 사천성이다.  
이 문제는 ```2017 카카오코드 본선``` 문제라고 한다.  
문제를 풀며 내 구현 능력이 많이 부족하다는 것을 느낄 수 있었다.  
문제를 차근차근 읽어보자.  

우리가 흔히 아는 사천성과 유사한 게임이다.  
```100 x 100```의 판이 최대 크기로 주어지게 되며,  
맞는 카드 짝을 찾아서 모두 없애면 되는 게임이다.  
하지만 기존 사천성과는 다르게 경로가 한 번 이상 꺾이면 안된다.  

문제를 어떻게 해결해야 할까?  

### 완전 탐색?
처음에는 완전탐색으로 해결을 해야 할까? 생각했다.  
그 이유는 짝을 짓는 순서에 따라 결과가 달라지기 때문이다.  
직접 모든 순서를 조합으로 구한 다음 짝을 없애주어야 한다고 생각했다.  

하지만 이는 너무 많은 시간이 소요된다.  
문제에서는 최대 ```26```가지의 알파벳이 등장할 수 있다고 했다.  
```26```개로 모든 조합을 만들어 짝을 짓는 것은 너무 복잡하다.  
따라서 완전탐색을 사용하는 문제는 아닌 것 같다.  

### DFS / BFS
우선 짝을 짓는데에는 DFS나 BFS를 사용하는 것이 맞다고 생각했다.  
경로를 검사하며 나아가야하니, 경로를 매회 저장해야 한다.  

그럼 DFS를 사용한다면 매개변수로 꺾은 횟수를 넘기면 되고,  
BFS를 사용한다면 큐에 꺾은 횟수를 함께 저장하면 될 것이다.  

### 단순 구현
짝을 지어주는 방법은 생각이 났으나, 순서를 결정할 방법이 떠오르지 않았다.  
결국 다른 사람들의 풀이를 참고하게 되었다.  

사실은 그냥 단순하게 구현을 하면 되는 것이었다.  
어차피 모든 카드는 알파벳으로 주어진다는 점을 이용한다.  
26칸의 배열에 알파벳 별로 위치를 저장하도록 하는 것이다.  
배열의 앞에서 부터 탐색을 하면, 자연스레 알파벳 순으로 정렬이 될 것이다.  

단, 우리는 ```while```문을 통해서 반복적으로 탐색을 해야 한다.  
이 부분에서 아까 내가 고민했던 짝을 지을 순서가 해결되는 것이다.  
```A~Z```까지 순서대로 탐색하며 현재 상황에 가능한 짝을 먼저 짓는 것이다.  

```BFS```로 탐색을 하며, 짝을 지을 수 있는 알파벳이라면?  
바로 해당 짝을 보드에서 지워주고, 배열 내에서도 삭제해준다!  
그럼 다음 ```iteration```에는 해당 짝이 걸리지 않을 것이다.  

최종적으로 아래와 같이 구현되었다.  
{% highlight cpp %}
int pair_count = 0;
bool validate_pair(int st_x, int st_y, int goal_x, int goal_y, vector<string> board) {
    int dx[4] = { -1, 0, 1, 0 };
    int dy[4] = { 0, 1, 0, -1 };
    queue<pii2> bfs_q;
    bfs_q.push({ {st_x, st_y}, {-1, 0} });
    // 좌표 x, y, 온 방향, 꺾은 횟수

    while (!bfs_q.empty()) {
        int curr_x = bfs_q.front().first.first;
        int curr_y = bfs_q.front().first.second;
        int curr_dir = bfs_q.front().second.first;
        int curr_turn = bfs_q.front().second.second;
        bfs_q.pop();

        for (int i = 0; i < 4; i++) {
            if (curr_dir != -1 && abs(curr_dir - i) == 2)
                continue; // 시작 위치가 아닌데 역방향으로는 갈 수 없음
            // 이는 visit 배열이 없기 때문!!

            int nx = curr_x + dx[i];
            int ny = curr_y + dy[i];
            int nx_turn = 0;
            
            if (curr_dir != -1 && curr_dir != i)
                nx_turn = curr_turn + 1;
            else
                nx_turn = curr_turn;
            
            if ((nx >= 0 && nx < board.size()) && (ny >= 0 && ny < board[0].size())) {
                if (nx_turn > 1)
                    continue; // 2번 이상 꺾으면 안됨

                if(board[nx][ny] == '.')
                    bfs_q.push({ {nx, ny}, {i, nx_turn} });
                else
                    if (nx == goal_x && ny == goal_y)
                        return true;
                    // 제거할 수 있는 패를 찾았으면 종료
            }
        }
    }

    return false;
    // 패를 찾지 못함
}

void read_board(vector<string> board, vector<vector<pii>> &char_list) {
    for (int i = 0; i < board.size(); i++) {
        for (int j = 0; j < board[0].size(); j++) {
            if (board[i][j] >= 'A' && board[i][j] <= 'Z') {
                char_list[board[i][j] - 'A'].push_back({ i, j });
                // 각 짝의 위치 저장
                pair_count++;
            }
        }
    }
}

string solution(int m, int n, vector<string> board) {
    string answer = "";
    
    vector<vector<pii>> char_list(26, vector<pii>());
    pair_count = 0;
    read_board(board, char_list);
    
    bool finded = true;
    while (finded) {
        finded = false;
        // 제거할 짝이 더 이상 없으면, 동작을 멈춰야 함

        for (int i = 0; i < 26; i++) {
            if (!char_list[i].empty()) {
                bool can_remove = validate_pair(char_list[i][0].first, char_list[i][0].second, char_list[i][1].first, char_list[i][1].second, board);
                if (can_remove) {
                    board[char_list[i][0].first][char_list[i][0].second] = '.';
                    board[char_list[i][1].first][char_list[i][1].second] = '.';
                    // 두 짝의 위치를 보드에서 빈 칸으로 표시해준다.
                    char_list[i].clear();
                    // 해당 짝 지워주기
                    pair_count -= 2;
                    answer += (i + 'A');
                    finded = true;
                    break;
                    // 해당 알파벳은 더 이상 탐색할 필요가 없으므로 탈출
                }
            }
        }
    }

    if (pair_count == 0)
        return answer;
    else
        return "IMPOSSIBLE";
}
{% endhighlight %}
