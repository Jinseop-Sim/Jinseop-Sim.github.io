---
layout: post
title: "프로그래머스 Level 3 - 수레 움직이기"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [수레 움직이기[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/250134)

이번 문제는 프로그래머스의 레벨3 수레 움직이기 문제이다.  
문제를 한 번 차근차근 읽어보자.  

최대 ```4x4``` 크기의 미로가 주어지고, 빨강 파랑 수레를 움직인다.  
둘은 같은 위치로 동시에 이동할 수 없고, 위치를 바꿀 수 없다.  
최소한의 턴을 사용해서 수레를 도착 지점까지 옮기도록 하라!  

문제는 요약하니 생각보다 짧다.  
하지만 관건은 어떻게 구현할 것인가!  

### BFS
가장 처음 생각한 것은 단순히 BFS를 사용하는 것이었다.  
빨간 수레와 파란 수레를 시작 지점에 모두 두고 동시에 움직인다.  
특이한 BFS로, 큐 두 개를 운용하며 동시에 움직이는 것이다.  
신경 써야할 사항들이 굉장히 많았다..  

이동을 진행하며, 둘이 같은 위치에 오는 경우엔 ```continue```한다.  
또한 다음 위치에 상대 수레가 있는 경우엔 ```continue```한다.  
둘 중 한 수레가 먼저 끝나게 되더라도, 다른 수레는 계속 이동해야 한다.  
두 수레가 모두 끝난 경우에만 두 수레가 소모한 턴 중 더 큰 값을 반환한다.  

최종적으로 아래와 같이 구현해보았다.  
{% highlight cpp %}
#include <string>
#include <vector>
#include <queue>
#include <iostream>
using namespace std;
#define pii pair<int, int>
#define piii pair<pair<int, int>, int>

int dx[4] = {-1, 0, 1, 0};
int dy[4] = {0, 1, 0, -1};
bool visit_red[4][4], visit_blue[4][4];
pii red_start, blue_start, red_end, blue_end;
int red_blue_bfs(vector<vector<int>> &maze){
    queue<piii> red_q;
    queue<piii> blue_q;
    
    red_q.push({red_start, 0});
    blue_q.push({blue_start, 0});
    // Queue 2개를 사용해서 검사한다면?
    visit_red[red_start.first][red_start.second] = true;
    visit_blue[blue_start.first][blue_start.second] = true;
    // 빨강 파랑 방문처리
    
    bool red_fin = false, blue_fin = false;
    int red_fin_time = 0, blue_fin_time = 0;
    int curr_red_x = 0, curr_blue_x = 0;
    int curr_red_y = 0, curr_blue_y = 0;
    int red_stride = 0, blue_stride = 0;
    
    while(true){
        if(red_q.empty() && blue_q.empty())
            break;
        
        if(!red_q.empty()){
            curr_red_x = red_q.front().first.first;
            curr_red_y = red_q.front().first.second;
            red_stride = red_q.front().second;
            red_q.pop();
        }
        
        if(!blue_q.empty()){
            curr_blue_x = blue_q.front().first.first;
            curr_blue_y = blue_q.front().first.second;
            blue_stride = blue_q.front().second;
            blue_q.pop();
        }
        
        if(curr_red_x == curr_blue_x && curr_red_y == curr_blue_y)
            continue;
            
        for(int i = 0; i < 4; i++){
            int red_nx = curr_red_x + dx[i];
            int red_ny = curr_red_y + dy[i];
            
            int blue_nx = curr_blue_x + dx[i];
            int blue_ny = curr_blue_y + dy[i];
            
            if(red_nx == curr_blue_x && red_ny == curr_blue_y)
                continue;
            if(blue_nx == curr_red_x && blue_ny == curr_blue_y)
                continue;
            
            if(red_nx == red_end.first && red_ny == red_end.second){
                red_fin = true;
                red_fin_time = red_stride + 1;
                curr_red_x = red_nx;
                curr_red_y = red_ny;
                red_stride = red_fin_time;
            }
            if(blue_nx == blue_end.first && blue_ny == blue_end.second){
                blue_fin = true;
                blue_fin_time = blue_stride + 1;
                curr_blue_x = blue_nx;
                curr_blue_y = blue_ny;
                blue_stride = blue_fin_time;
            }
            
            if(!red_fin){
                if((red_nx >= 0 && red_nx < maze.size()) && (red_ny >= 0 && red_ny < maze[0].size())){
                    if(!visit_red[red_nx][red_ny] && maze[red_nx][red_ny] != 5){
                        red_q.push({ {red_nx, red_ny}, red_stride + 1});
                        visit_red[red_nx][red_ny] = true;
                    }
                }
            }
            
            if(!blue_fin){
                if((blue_nx >= 0 && blue_nx < maze.size()) && (blue_ny >= 0 && blue_ny < maze[0].size())){
                    if(!visit_blue[blue_nx][blue_ny] && maze[blue_nx][blue_ny] != 5){
                        blue_q.push({ {blue_nx, blue_ny}, blue_stride + 1});
                        visit_blue[blue_nx][blue_ny] = true;
                    }
                }
            }
        }
    }
    
    if(!red_fin || !blue_fin)
        return 0;
    
    return max(red_fin_time, blue_fin_time);
}
{% endhighlight %}  

아쉽게도 절반만 맞춰 정답을 받을 수 없었다.  
사실 코드가 굉장히 지저분하고, 조건이 모두 제대로 처리되지 않았다.  
그리고 BFS만 사용했을 때 가장 중요한 점을 놓치고 있었다.  
바로 BFS만으로는 모든 경우의 수를 커버할 수 없다는 점이다.  

우리는 빨강 파랑 수레가 움직이는 모든 경우의 수를 생각해야 한다.  
따라서 단순히 BFS만 사용해서 푸는 것에는 무리가 있다.  

### 백트래킹
그래서 다음으로 생각난 방식은, BFS + 백트래킹이다.  
BFS로 이동할 수 있는 모든 경우는 수레당 4 방향이다.  
즉, 두 수레를 모두 동시에 이동 시키는 경우는 16개가 최대이다.  
미로도 최대 ```4x4```칸 밖에 되지 않으니 시간은 충분할 것 같다.  

생각해야 할 조건은 위에서 BFS를 진행한 방식과 동일하게 아래와 같다.  
1. 미로 밖으로 나가는가?
2. 수레가 서로 같은 위치로 이동하는가?
3. 수레가 서로 자리를 바꾸는가?
4. 수레가 장애물을 만났는가?
5. 수레가 이미 방문한 곳을 다시 방문 하였는가?

위의 조건에서 마지막 ```visit``` 부분은 도착 지점이 예외가 된다.  
두 수레 중 하나가 먼저 도착하면, 해당 지점에서 움직일 수 없다.  
따라서 도착 지점은 예외적으로 방문했어도 걸러내지 않는다.  

백트래킹 로직은 두 수레가 모두 도착했을 때, 현재 걸음 수를 반환하도록 한다.  
우리는 가장 작은 걸음 수를 반환해야 하므로, 최소 걸음을 갱신하며 진행한다.  
함수 내에 ```answer``` 변수를 만들어 최소 걸음을 저장하며 진행하고,  
마지막에 함수 종료 시 ```answer```를 최종적으로 반환하도록 한다.  

최종적으로 아래와 같이 구현되었다.
{% highlight cpp %}
int backtrack_wagen(int red_x, int red_y, int blue_x, int blue_y, int stride, vector<vector<int>>& maze) {
    if (red_fin && blue_fin)
        return stride;
    int answer = MAX;

    for (int i = 0; i < 4; i++) {
        for (int j = 0; j < 4; j++) {
            int red_nx = red_x + dx[i];
            int red_ny = red_y + dy[i];
            int blue_nx = blue_x + dx[j];
            int blue_ny = blue_y + dy[j];

            if (red_fin) {
                red_nx = red_end.first;
                red_ny = red_end.second;
            }
            if (blue_fin) {
                blue_nx = blue_end.first;
                blue_ny = blue_end.second;
            }

            if ((red_nx < 0 || red_nx >= maze.size()) || (red_ny < 0 || red_ny >= maze[0].size()))
                continue;
            if ((blue_nx < 0 || blue_nx >= maze.size()) || (blue_ny < 0 || blue_ny >= maze[0].size()))
                continue; // 미로 밖으로 나가버리는 경우 불가능
            if (maze[red_nx][red_ny] == 5 || maze[blue_nx][blue_ny] == 5)
                continue; // 둘 중 하나라도 장애물 만나는 경우 불가능
            if ((red_nx == blue_x && red_ny == blue_y) && (blue_nx == red_x && blue_ny == red_y))
                continue; // 둘의 자리가 바뀌는 경우
            if ((!red_fin && visit_red[red_nx][red_ny]) || (!blue_fin && visit_blue[blue_nx][blue_ny]))
                continue; // 도착 지점은 예외, 방문 이미 한 곳은 이동 불가

            visit_red[red_nx][red_ny] = true;
            visit_blue[blue_nx][blue_ny] = true;
            if (red_nx == red_end.first && red_ny == red_end.second)
                 red_fin = true;
            if (blue_nx == blue_end.first && blue_ny == blue_end.second)
                 blue_fin = true;

            answer = min(answer, backtrack_wagen(red_nx, red_ny, blue_nx, blue_ny, stride + 1, maze));

            red_fin = false;
            blue_fin = false;
            visit_red[red_nx][red_ny] = false;
            visit_blue[blue_nx][blue_ny] = false;
        }
    }

    return answer;
}
{% endhighlight %}

풀고 나니 생각보다 꽤 간단한 문제였던것 같다.  
하지만 큰 의문이 남았는데, ```dx, dy```에 대한 문제이다.  
```dx, dy```의 순서에 따라 정답을 받지 못하는 경우도 있다.  
이 부분은 무엇이 문제인지 좀 더 생각해보자.  
