---
layout: post
title: "프로그래머스 Level 2 - 양궁 대회"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [양궁 대회[LEVEL 2]](https://school.programmers.co.kr/learn/courses/30/lessons/92342)

이번 문제는 프로그래머스의 레벨 2 문제 양궁 대회문제이다.  
문제를 차근차근 한 번 읽어보도록 하자.  

라이언과 어피치가 서로 양궁 시합을 진행한다.  
```1 ~ 10```점 까지의 점수에 대해 더 많이 맞춘 사람이 점수를 얻는다.  
단, 동일한 수의 화살을 맞춘 경우 어피치가 점수를 가져간다.  

어피치가 맞춘 점수와 화살의 수가 주어질 때,  
라이언이 점수차를 최대로 하여 어피치를 이길 수 있는 방법을 찾아라.  
```Tie break```일 때, 낮은 점수를 더 많이 맞춘 경우를 채택한다.  
문제를 어떻게 해결할 수 있을까?  

### Greedy
문제를 차근차근 읽어 보았을 때, Greedy의 느낌이 굉장히 강하다.  
어피치보다 반드시 화살을 해당 점수에 많이 맞춰야 하기 때문에  
순서대로 화살을 다 맞추며 뒤로 가면 되지 않을까? 하는 생각이 든다.  

하지만 그 방법은 문제가 있다.  
예를 들어, 어피치가 ```2 1 1 1 0 0 0 0 0 0```의 점수를 쏘았을 때  
라이언은 ```3 2 0 0 0 0 0 0 0 0```을 맞추면, 어피치를 이길 수 있다.  
하지만 ```0 2 2 0 1 0 0 0 0 0```을 맞추면, 더 큰 차이로 어피치를 이긴다.  
앞에서 부터 순서대로 채우며 나아가는 것은 이런 경우를 찾지 못한다.  

위와 같은 경우를 커버하려면 어피치의 점수를 미리 다 알고 대응해야 한다.  
즉, Greedy로 풀기에는 뭔가 로직에 허점이 많아 보인다.  

### 완전탐색
백트래킹을 이용한 완전탐색은 불가능할까?  
시간 복잡도를 생각해보자.  

배열의 길이는 10으로 항상 고정되어 있다.  
앞에서부터 중복 없이 모두 선택하는 것으로 볼 수 있다.  
화살이 예를 들어 5발이면 ```10 9 8 7 6``` ```9 8 7 6 5```와 같이 말이다.  
또한 라이언이 선택하는 경우는 어피치의 결과에 대해 종속적이므로  
나올 수 있는 경우의 수가 그렇게 많지는 않을 것으로 예상된다.  

한 번 백트래킹을 이용해 구현해보도록 하자!  
우리는 ```남은 화살, 어피치 점수, 라이언 점수```를 신경쓰며 진행해야 한다.  
매개변수로 세 변수를 넘기며, ```남은 화살```이 없을 때 중단하도록 한다.  
중단 되었을 때, 라이언의 가장 큰 차이의 점수로 이기는 경우로 갱신하는 것이다.  

```Tie break```를 처리하는 데에 굉장히 애를 먹었다.  
__가장 낮은 점수를 많이 맞추는 경우__ 를 우선으로 선택해야 하는데,  
처음에는 당연히 마지막에 골라지는 것이 낮은 점수를 많이 고른게 아닐까했다.  

하지만 그게 아니었다, __가장 낮은__ 점수를 많이 맞춘 경우,  
즉 ```2 1 1 0 0```과 ```9 0 0 0 1```이 답일 때 후자를 선택하라는 것이다.  
그럼 당연히 마지막에 골라지는 것만이 답이 되는 것은 아니게 된다.  

결국 굉장히 지저분한 분기문으로 처리했으며  
최종적으로 아래와 같이 구현되었다.  
{% highlight cpp %}
vector<int> score_vec(11, 0);
vector<int> result(11, 0);
int max_diff = 0, max_last_idx = 0;
bool is_possible = false;
void backtrack(int curr_idx, int arrows, int apeach_score, int ryan_score, vector<int> &info) {
    if (arrows == 0) {
        if (ryan_score > apeach_score) {
            is_possible = true;
            int diff = ryan_score - apeach_score;

            if (max_diff <= diff) {
                if (max_diff == diff) {
                    if (curr_idx >= max_last_idx) {
                        if (max_last_idx == curr_idx) {
                            if (result[curr_idx] <= score_vec[curr_idx])
                                result = score_vec;

                            return;
                        }

                        max_last_idx = curr_idx;
                        result = score_vec;
                    }

                    return;
                }

                max_last_idx = curr_idx;
                max_diff = diff;
                result = score_vec;
            }
        }

        return;
    }

    for (int i = curr_idx + 1; i < info.size(); i++) {
        if (arrows - (info[i] + 1) < 0)
            score_vec[i] += arrows;
        else
            score_vec[i] = info[i] + 1;

        int apeach_coef = 0, ryan_coef = 1;
        if (info[i] < score_vec[i]) {
            // 라이언이 이기면 계수를 1로 만들어야 함
            apeach_coef = 1;

            if (info[i] == 0) // 근데 어피치가 안쏜거면 0으로 다시 만듬
                apeach_coef = 0;
        }
        else // 어피치가 이기면 라이언 계수가 0이 됨
            ryan_coef = 0;

        backtrack(i, arrows - score_vec[i], apeach_score - ((10 - i) * apeach_coef), ryan_score + ((10 - i) * ryan_coef), info);
        score_vec[i] = 0;
    }
}
{% endhighlight %}  

사실 이 문제는 예전에 풀었던 문제이다.  
그 때는 다른 사람의 풀이를 참고하며 풀었었던 기억이 있다.  
이번에는 다시 푸는 것이니 혼자 힘으로 풀어내려 했다.  
다행히도 혼자 풀 수 있었지만, 코드가 굉장히 지저분하다.  
좀 더 깔끔하게 짤 수 있는 로직을 생각하는 연습을 해야할 것 같다.  
