---
layout: post
title: "프로그래머스 Level 3 - 등산 코스 정하기"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [등산 코스 정하기[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/118669)

이번 문제는 프로그래머스의 레벨 3 등산 코스 정하기 문제이다.  
```2022 KAKAO INTERNSHIP``` 문제라고 한다.  
문제를 차근차근 한 번 읽어보도록 하자.  

최대 ```50000```개의 체크포인트가 주어지며, 등산 코스를 설계해야 한다.  
또한 모든 체크포인트가 출입구 혹은 봉우리가 될 수 있다.  
등산코스는 출입구로 출발하여 봉우리를 반드시 1개 거쳐야 한다.  
또한, 반드시 들어왔던 출입구로 동일하게 나가야 한다.  

이동 시간을 최소로 하는 등산 코스를 설계하라!  

### Dijkstra
우선 이동 시간을 최소로 하는 경로를 구하려면?  
최단 경로 알고리즘인 ```Dijkstra```를 이용해야 할 것 같다.  
그럼 우선 간선의 정보를 ```인접 리스트```의 형태로 받아보자.  
아래와 같이 정보를 받고, ```Dist``` 배열을 초기화 해주도록 하자.  

{% highlight cpp %}
vector<pii> graph[50001];
ll dist[50001];
void init_dist(int n) {
    for (int i = 1; i <= n; i++)
        dist[i] = INF;
}

vector<int> solution(int n, vector<vector<int>> paths, vector<int> gates, vector<int> summits) {
    vector<int> answer;

    for (int i = 0; i < paths.size(); i++) {
        int from = paths[i][0];
        int to = paths[i][1];
        int cost = paths[i][2];

        graph[from].push_back({ to, cost });
        graph[to].push_back({ from, cost });
    }

    return answer;
}
{% endhighlight %}

다익스트라를 사용할 수 있는 기본 조건은 다 갖추었다.  

### 제약 조건
이제 문제의 제약 조건을 구현해야 할 차례이다.  
먼저 반드시 출입구로 들어가야 하며, 중간에 출입구를 거칠 수 없다.  
또한 들어왔던 출입구로 반드시 나가야 한다.  

우선 가장 처음에 큐에 출입구를 모두 집어넣음으로 시작할 수 있다.  
동시에 ```Dist``` 배열의 모든 출입구를 ```0```으로 초기화한다.  

또 하나의 조건, 봉우리 조건이 문제이다.  
우리는 반드시 단 하나의 봉우리만을 지나야 한다.  
이 부분에서 계속 고민을 하다 풀이를 결국 참고하게 되었다.  

핵심 아이디어는 아래와 같다.  
__우리가 구하는 것은 전체 경로의 시간이 아닌 Intensity이다!__  
즉, 굳이 우리가 지나야 하는 경로를 훑을 필요가 없다는 말이다.  

자세히 말하면, ```출입구 -> 봉우리```의 경로에 대한 ```Intensity```를 갱신하는 것이다.  
그렇게 모든 ```출입구 -> 봉우리```에 대한 편도 경로를 훑고 나면 끝이다!  
그 중에서 최소가 되는 ```Intensity```와 ```Summit``` 값을 답으로 제출하면 된다.  

예를 들어, ```1 -> 봉우리``` 경로가 최소 ```Intensity```를 가지게 된다면?  
다른 어떤 경로로 하산을 해도 결국 최소 ```Intensity```는 변하지 않는다.  
이러한 원리로 인해 위의 로직을 사용할 수 있는 것이다.  

최종적으로 아래와 같이 구현되었다.  
{% highlight cpp %}
#define pii pair<int, int>
#define INF 987654321
#define ll long long

vector<pii> graph[50001];
ll dist[50001];
bool summit_checker[50001];
void init_dist(int n) {
    for (int i = 1; i <= n; i++)
        dist[i] = INF;
}

void init_summit(vector<int> summits) {
    for (int i = 0; i < summits.size(); i++)
        summit_checker[summits[i]] = true;
}

void do_dijkstra(vector<int> &answer, vector<int> gates) {
    priority_queue<pii, vector<pii>, greater<>> dijk_q;
    int min_summit = INF, min_intensity = INF;

    for (int i = 0; i < gates.size(); i++) {
        dijk_q.push({ 0, gates[i]});
        // 출입구들을 모두 큐에 집어넣고, 비용은 0
        dist[gates[i]] = 0; // 출입구들은 모두 거리 0으로 초기화
    }

    while (!dijk_q.empty()) {
        int curr_node = dijk_q.top().second;
        int curr_cost = dijk_q.top().first;
        dijk_q.pop();

        if (curr_cost > dist[curr_node])
            continue;

        if (summit_checker[curr_node]) {
            // 꼭대기에 도착했다면?
            if (curr_cost <= min_intensity) {
                min_intensity = curr_cost;
            // 현재 intensity가 저장된 값보다 작으면 갱신
                if (curr_cost == min_intensity)
                    min_summit = min(curr_node, min_summit);
                else
                    min_summit = curr_node;
            // tie break의 경우에는 더 값이 작은 산봉우리 쪽으로
            }

            continue; // 꼭대기에 도착했으면 더 이상 탐색 X
        }

        for (int i = 0; i < graph[curr_node].size(); i++) {
            int next_node = graph[curr_node][i].first;
            int next_cost = graph[curr_node][i].second;

            if (dist[next_node] > max(next_cost, curr_cost)) {
                dist[next_node] = max(next_cost, curr_cost);
                dijk_q.push({ dist[next_node], next_node});
            }
        }
    }

    answer.push_back(min_summit);
    answer.push_back(min_intensity);
}

vector<int> solution(int n, vector<vector<int>> paths, vector<int> gates, vector<int> summits) {
    vector<int> answer;

    for (int i = 0; i < paths.size(); i++) {
        int from = paths[i][0];
        int to = paths[i][1];
        int cost = paths[i][2];

        graph[from].push_back({ to, cost });
        graph[to].push_back({ from, cost });
    }

    init_dist(n);
    init_summit(summits);
    do_dijkstra(answer, gates);

    return answer;
}
{% endhighlight %}
