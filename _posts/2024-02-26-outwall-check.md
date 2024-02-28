---
layout: post
title: "프로그래머스 Level 3 - 외벽 점검"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [외벽 점검 [LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/60062)

이번 문제는 프로그래머스의 레벨 3 문제 외벽 점검이다.  
2020 카카오 공채 코딩테스트 문제였다고 한다.  
꽤 어려울 것 같으니 문제를 차근차근 읽어보자.  

최대 ```200```의 외벽 길이가 주어진다.  
이 때 최대 ```8```명의 친구와 이동 가능 거리가 주어지는데,  
각 친구들은 ```100```까지 움직일 수 있다.  
또한 취약 외벽이 주어지는데, ```0 ~ 199```까지의 위치로 주어진다.  

친구들은 ```시계 혹은 반시계``` 방향으로 움직일 수 있다.  
외벽의 취약점을 모두 점검할 수 있는 최소한의 친구 수를 구해라!  

문제가 간단하지만, 꽤 어려운 아이디어가 필요해 보인다.  
어떻게 문제를 풀어야할까?  

### Greedy?
문제에 예시로 나와있는 그림을 보니 문득 그리디인가? 하는 생각이 들었다.  
아래의 예시 그림을 한 번 보도록 하자.  

![image](https://github.com/Jinseop-Sim/Jinseop-Sim.github.io/assets/71700079/1d598af6-12d2-4396-bfc2-a23e4dd37969)  

붉은 경로는 내가 표시한 최단 경로이다.  
위의 그림과 같이 모든 위치에 대해서 최단 거리를 알아낼 수 있다면?  

```0```번 위치부터 ```for```문을 돌며 ```시계, 반시계```를 각각 돌아보자.  
그럼 둘 중 다른 정점을 처음 만나는 거리가 더 짧은 방향을 선택한다.  

또한, 어차피 가동 거리가 가장 긴 친구부터 선택하는 것이 이득이다.  
거리가 긴 취약 지점을 거리가 가장 긴 친구가 처리하는 것이 좋기 때문이다.  
그럼 우리가 구해야 할 것은, 거리가 가장 긴 취약 지점을 알아야 한다.  

하지만 취약 지점에 대한 계산은 그리디하게 구할 수 없다고 판단했다.  
그렇게 하려면, 우선 모든 취약 지점 간 거리를 직접 계산해야 한다.  
또한 취약 지점이 겹쳐지는 경우에 대한 판단이 애매해 질 것 같다고 생각했다.  
따라서, 모든 취약 지점에 대해 순회를 해보기로 판단했다.  
아래와 같은 순서로 로직을 짜보려고 한다.  

1. 기본적으로 취약 지점을 원형으로 만들어 준다.
  - 이 부분은 배열 뒤에 ```취약 지점 + n```을 삽입해 구현한다.
2. ```for```문을 통해 모든 취약 지점에서부터 시작해본다.
  - 핵심은 시계, 반시계를 구분할 필요가 없다는 점이다.
  - 따라서 한 방향으로만 모든 취약 지점을 시작지점으로 두어본다.
3. 각 시작지점에 대해, 가동 거리가 긴 친구부터 검사를 시켜본다.
4. 모든 친구에게 검사를 시켰지만 순회가 불가능한 경우, ```-1```을 반환한다.

### 완전 탐색!
결국 다른 사람들의 풀이를 조금 찾아보았다.  
설마설마 했는데 완전탐색으로 풀 수 있는 문제였다.  
```시계, 반시계```로 가는 모든 경우를 구해야 한다고 생각했는데 아니었다.  
문제를 꽤 단순화한 뒤 완전탐색으로도 시간이 충분했다.  

문제의 풀이 과정은 아래와 같다.  


최종적으로 아래와 같이 구현되었다.  
{% highlight cpp %}
vector<int> permutation;
int result = INF;
bool visit[8];
void circulate_weak(int n, vector<int> &weak) {
    int size = weak.size();
    for (int i = 0; i < size - 1; i++)
        weak.push_back(weak[i] + n);
    // 취약 지점을 원형으로 만들기 위해 뒤에 값을 추가한다.
    // 마지막 지점을 제외한 나머지 지점 추가
}

void backtrack(int depth, int idx, int limit, vector<int> dist, vector<int> weak) {
    if (depth == dist.size()) {
        for (int i = 0; i < limit; i++) {
            int from = weak[i]; // 출발 지점
            int end = weak[i + limit - 1];
            // 종료 지점은 출발지에 대해 정반대편

            for (int j = 0; j < permutation.size(); j++) {
                from += permutation[j]; // 시작 지점부터 탐색 가능 거리 더하기
                
                if(j + 1 > result)
                    break;
                
                if (from >= end) {
                // 시작지점 >= 끝지점이 되면 순찰을 완료한 것
                    result = min(result, j + 1);
                    break;
                }

                
                for (int k = i + 1; k < i + limit; k++) {
                    if (weak[k] > from) {
                        from = weak[k];
                        break;
                    }
                } // 외벽 점검이 끝나지 않았지만, 이동할 수 없는 경우
                // 다음 취약 지점으로 이동해서 친구를 움직여야 한다.
            }
        }

        return;
    }

    for (int i = 0; i < dist.size(); i++) {
        if (!visit[i]) {
            visit[i] = true;
            permutation.push_back(dist[i]);
            backtrack(depth + 1, i, limit, dist, weak);
            visit[i] = false;
            permutation.pop_back();
        }
    }
}

int solution(int n, vector<int> weak, vector<int> dist) {
    int answer = 0;

    sort(dist.begin(), dist.end(), greater<>());
    // 친구들 이동거리 내림차순 정렬
    int origin_weak_size = weak.size();
    circulate_weak(n, weak);

    for (int i = 0; i < dist.size(); i++) {
        visit[i] = true;
        permutation.push_back(dist[i]);
        backtrack(1, i, origin_weak_size, dist, weak);
        visit[i] = false;
        permutation.pop_back();
    }

    if (result == INF)
        return -1;
    else
        answer = result;
    return answer;
}
{% endhighlight %}
