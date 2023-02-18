---
layout: post
title: "프로그래머스 Level 2 - 숫자 변환하기"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [숫자 변환하기[LEVEL 2]](https://school.programmers.co.kr/learn/courses/30/lessons/154538)  

### DFS
처음엔 DFS로 단순히 완전 탐색을 하려고 했었다.  
아무 생각 없이 정한 바보같은 생각이었다.  
당연하게도 너무 많은 가짓 수가 나오기에, 시간초과가 발생했다.  

{% highlight cpp %}
#include <string>
#include <vector>
using namespace std;
#define BIGINT 9999999

int res = BIGINT;
void recur(int x, int y, int n, int depth) {
    if (x == y) {
        if (res > depth)
            res = depth;

        return;
    }

    if (x > y) return;

    recur(x + n, y, n, depth + 1);
    recur(x * 2, y, n, depth + 1);
    recur(x * 3, y, n, depth + 1);
}

int solution(int x, int y, int n) {
    int answer = 0;
    recur(x, y, n, 0);

    if (res == BIGINT)
        answer = -1;
    else
        answer = res;

    return answer;
}
{% endhighlight %}  

이렇게 코드를 짜면, 3 + 9 + 27 + ... 와 같이 재귀하게 된다.  
이는 등비수열의 합이므로, O(3^N)의 복잡도를 갖게 된다.  
심지어 ```x```와 ```y``` 사이의 간격이 멀 수록 더욱 시간이 걸린다.  

### BFS
그럼 DFS보다 조금 더 빠르게 해결이 가능한 BFS는 어떨까?  
```visit[]``` 배열과 함께 BFS를 사용함으로써 해결했다.  

{% highlight cpp %}
#include <string>
#include <vector>
#include <queue>
using namespace std;

int res = 0;
bool visit[1000001];

queue<pair<int, int>> bfs_q;
void bfs(int x, int y, int n) {
    bfs_q.push({ x, 0 });
    visit[x] = true;

    while (!bfs_q.empty()) {
        int curr = bfs_q.front().first;
        int depth = bfs_q.front().second;
        bfs_q.pop();

        if ((curr + n == y) || (curr * 2 == y) || (curr * 3 == y)) {
            res = depth + 1;
            break;
        }
        else {
            if ((curr + n < y) && !visit[curr + n]) {
                bfs_q.push({ curr + n, depth + 1 });
                visit[curr + n] = true;
            }
            if ((curr * 2 < y) && !visit[curr * 2]) {
                bfs_q.push({ curr * 2, depth + 1 });
                visit[curr * 2] = true;
            }
            if ((curr * 3 < y) && !visit[curr * 3]) {
                bfs_q.push({ curr * 3, depth + 1 });
                visit[curr * 3] = true;
            }
        }
    }
}

int solution(int x, int y, int n) {
    if (x == y) return 0;

    int answer = 0;
    bfs(x, y, n);

    if (res == 0)
        answer = -1;
    else
        answer = res;

    return answer;
}
{% endhighlight %}

```visit[]``` 배열을 통해 시간을 줄이는 것이 핵심!  
사실 DFS도 ```visit[]``` 배열과 함께 돌렸으면, 통과했을 것 같다.  

### Bonus : DP
처음에 DP를 생각하지 않았던 것은 아니다.  
하지만 어떻게 __Memoization__ 할 지 생각이 나지 않았었다.  
다른 사람이 DP로 푼 것을 참고해 아래와 같이 풀었다.  

{% highlight cpp %}
#include <string>
#include <vector>
using namespace std;
#define BIGINT 9999999

int DP[1000001];
void fill_DP(int x, int y, int n) {
    for (int i = x + 1; i < y + 1; i++) {
        int key_a = BIGINT, key_b = BIGINT, key_c = BIGINT, kind = 0;

        if ((i % 2 == 0) && (i / 2 >= x))
            key_a = DP[i / 2];
        if ((i % 3 == 0) && (i / 3 >= x))
            key_b = DP[i / 3];
        if ((i - n) >= x)
            key_c = DP[i - n];

        kind = min(min(key_a, key_b), key_c);
        // 가능한 방법 중 최소를 저장하면서 나아간다.  

        if (kind == BIGINT)
            DP[i] = BIGINT;
        else
            DP[i] = kind + 1;
    }
}

int solution(int x, int y, int n) {
    fill_DP(x, y, n);

    if (DP[y] == BIGINT)
        return -1;
    else
        return DP[y];
}
{% endhighlight %}  

1차원 배열을 순차적으로 나아가며, 반대로 확인한다.  
```x```가 기준이 아닌, 현재 숫자가 ```x```가 될 수 있는가?  
가능하다면 이전의 값에 1을 더하고, 아니면 ```BIGINT```를 저장한다.  