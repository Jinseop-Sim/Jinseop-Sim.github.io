---
layout: post
title: "프로그래머스 Level 3 - GPS"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---

굉장히 어려운 문제였다.  
정확한 풀이가 도저히 떠오르지 않았다.  

### 1차 시도 : 완전 탐색

주어진 Graph를 Adjacent List로 구현한 다음, 완전 탐색을 시켰다.  
끊어진 부분을 찾으면 그 index를 저장해 수정가능한 지 판단토록 했다.  
하지만 큰 문제로, 끊어진 구간의 길이가 2 이상이면 찾을 수가 없었다.

{% highlight cpp %}
#include <iostream>
#include <vector>
using namespace std;

vector<vector<int>> make_map(int n, vector<vector<int>> edge_list) {
    vector<vector<int>> maps(n+1);
    for (auto i : edge_list) {
        maps[i[0]].push_back(i[1]);
        maps[i[1]].push_back(i[0]);
    }

    return maps;
}

bool pos_revise(vector<vector<int>> maps, vector<int> gps_log, int rev_idx) {
    for (int i = 0; i < maps[gps_log[rev_idx] - 1].size(); i++) {
        for (int j = 0; j < maps[gps_log[rev_idx] + 1].size(); j++) {
            if (maps[gps_log[rev_idx] - 1][i] == maps[gps_log[rev_idx] + 1][j]) 
                return true;
        }
    }

    return false;
}

int check_log(vector<vector<int>> maps, vector<int> gps_log) {
    int rev_idx = 0, cnt = 0;

    for (int i = 0; i < gps_log.size()-1; i++) {
        bool is_connect = true;
        for (int j = 0; j < maps[gps_log[i]].size(); j++) {
            if (gps_log[i + 1] == maps[gps_log[i]][j] || gps_log[i] == gps_log[i + 1]) break;
            if (j == maps[gps_log[i]].size() - 1) {
                rev_idx = i;
                is_connect = false;
            }
        }

        if (!is_connect) {
            bool is_rev = pos_revise(maps, gps_log, rev_idx);
            if (is_rev) cnt++;
            else return -1;
        }
    }

    if (cnt == 0) return 0;
    else return cnt;
}

int solution(int n, int m, vector<vector<int>> edge_list, int k, vector<int> gps_log) {
    int answer = 0;
    vector<vector<int>> maps(n+1);

    maps = make_map(n, edge_list);
    answer = check_log(maps, gps_log);

    return answer;
}
{% endhighlight %}

### 2차 시도 : DP

이런 생각은 어떻게 해내는 건지 정말 놀랍다.  
DP로 풀어나가는데, 배열을 채워 나가는 방식은 다음과 같다.  
- i번째 로그가 j가 될 때, 로그를 얼마나 고쳐야 될까?
  - 그것은 i-1 번째 로그가 j에 연결된 인접 노드들을 지나오며 고친 횟수 중 최소일 것이다.
- 제자리에 가만히 있는 경우도 생각해 줄 것! (```DP[i-1][j]```)


{% highlight cpp %}
#include <iostream>
#include <vector>
using namespace std;
#define INF 100000000

vector<vector<int>> make_map(int n, vector<vector<int>> edge_list) {
    vector<vector<int>> maps(n + 1);
    for (auto i : edge_list) {
        maps[i[0]].push_back(i[1]);
        maps[i[1]].push_back(i[0]);
    }

    return maps;
}

void set_base(vector<vector<int>>& DP, vector<int> gps_log) {
    DP[0][gps_log[0]] = 0;
}

int solution(int n, int m, vector<vector<int>> edge_list, int k, vector<int> gps_log) {
    int answer = 0;
    vector<vector<int>> maps(n + 1);
    vector<vector<int>> DP(k, vector<int>(n + 1, INF));
    // i번째 Log가 j일 때, 해당 로그로 오기까지 고쳐야 하는 로그의 최소 횟수

    maps = make_map(n, edge_list);
    set_base(DP, gps_log); // 시작 경로와 끝 경로 고정

    // 시작 노드를 지난 2번째 자리부터 탐색
    for (int i = 1; i < k; i++) {
        // Log의 i번째 자리가 j일 때, 최소 고침 수를 이전 로그에서 가져온다.
        for (int j = 1; j <= n; j++) {
            DP[i][j] = min(DP[i - 1][j], DP[i][j]);
            // 이동하지 않고 그 자리에 가만히 있는 경우
            for (int node : maps[j])
                DP[i][j] = min(DP[i - 1][node], DP[i][j]);
            // j에 연결된 인접 노드에서 j로 온 경우

            DP[i][j] += (gps_log[i] == j) ? 0 : 1;
            // 만약 로그를 고친 경로라면, 횟수 더해주기.
        }
    }

    if (DP[k - 1][gps_log[k - 1]] >= INF) return -1;
    answer = DP[k - 1][gps_log[k - 1]];

    for (auto i : DP) {
        for (auto k : i) cout << k << " ";
        cout << '\n';
    }
    return answer;
}
{% endhighlight %}