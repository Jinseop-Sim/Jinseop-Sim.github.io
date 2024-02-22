---
layout: post
title: "프로그래머스 Level 3 - 카운트 다운"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [카운트 다운[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/131129)

이번 문제는 프로그래머스 레벨 3 문제인 카운트 다운이다.  
문제를 차근차근 한 번 읽어보도록 하자.  

```1 ~ 100,000``` 점의 점수가 주어진다.  
이 때 다트를 맞춰 주어진 점수를 0점까지 깎아야 한다.  
맞출 수 있는 점수는 ```1 ~ 20```까지의 점수가 가능하며,  
```트리플, 더블```을 맞추게 되면 해당 점수에 ```X3, X2```가 된다.  
```불```을 맞추게 되면 ```50```점을 깎을 수 있다.  

이 때 최소로 다트를 던져 이길 수 있는 경우를 구하라!  
단, 동일한 횟수에 대해서는 ```싱글, 불```을 많이 맞춘 사람이 이기게 된다.  

### DFS? BFS?
보자마자 생각난 것은 이전에 풀었던 ```양궁 대회``` 문제였다.  
그 때는 ```DFS```를 이용해서 문제를 풀었는데, 비슷한 문제라고 느꼈다.  

그럼 DFS와 BFS 둘 중 어느 알고리즘을 사용할 지 어떤 기준으로 정할까?  
나는 보통 최대한 많은 경우를 탐색해 보아야 하는 경우에 DFS를 사용한다.  
BFS는 보통 가장 처음 해가 찾아지는 ```depth```가 답이 될 때 사용한다.  

이 문제는 가장 처음 해가 찾아지는 게 아닌, 많은 경우를 살펴봐야 한다.  
왜? 동일한 횟수로 ```0```을 만들 수 있는 다른 경우가 있을 수 있기 때문이다.  
따라서 나는 DFS를 사용해보기로 결심했다.  

### DFS
만약 DFS를 사용해서 문제를 풀었을 때 시간초과는 발생하지 않을까?  
우선, 재귀적으로 들어갈 수 있는 경우는 총 4가지라고 생각된다.  

1. 불 (50점)
2. 트리플 (X3)
3. 더블 (X2)
4. 싱글 (1 ~ 20)

위의 4가지 경우로 재귀를 타고 들어가야 한다.  
또한, 각 점수는 ```1 ~ 20```점을 얻을 수 있으므로 ```for```문을 이용해야 한다.  
사실상 모든 경우를 들르는 시간 복잡도는 ```O(3^20 + 불의 경우의 수)```가 된다.  
시간 초과가 나지 않을 수가 없다고 생각이 된다..  

그래도 최대한 시간 초과를 줄이기 위해 아래와 같은 방법을 시도해 본다.  
1. 점수를 최대한 많이 얻기 위해 20점부터 계산한다. (Greedy)
  - 재귀 순서 또한 ```트리플 -> 더블 -> 싱글``` 순서로 재귀하도록 한다.
2. 최초로 0을 만들었을 때의 던진 횟수보다 더 많이 던지는 순간 재귀를 멈춘다.
3. 점수가 0 이하로 내려가는 순간 재귀를 멈춘다.  

위와 같이 가지치기를 한다면 한 번 시도해볼만 하지 않을까 싶다.  
아래와 같이 구현해 보았다.  
{% highlight cpp %}
int min_dart_count = 987654321, max_single_bool = 0;
void play_dart(int score, int dart_count, int single_bool) {
    if (score < 0)
        return; // 버스트
    if (dart_count > min_dart_count)
        return; // 넘어가면 탐색할 필요 없음

    if (score == 0) {
        if (min_dart_count >= dart_count) {
            if (min_dart_count == dart_count) {
                max_single_bool = max(single_bool, max_single_bool);
                return; // 같은 경우엔 더 싱글/불이 더 큰 경우로
            }

            min_dart_count = dart_count;
            max_single_bool = single_bool;
            // 다른 경우에는 그럼 싱글/불도 초기화 해야함
        }

        return;
    }

    play_dart(score - 50, dart_count + 1, single_bool + 1); // 불
    for (int i = 20; i >= 1; i--) {
        play_dart(score - (i * 3), dart_count + 1, single_bool); // 트리플
        play_dart(score - (i * 2), dart_count + 1, single_bool); // 더블
        play_dart(score - i, dart_count + 1, single_bool + 1); // 싱글
    }
}

vector<int> solution(int target) {
    vector<int> answer;

    play_dart(target, 0, 0);
    answer.push_back(min_dart_count);
    answer.push_back(max_single_bool);

    return answer;
}
{% endhighlight %}  

안타깝게도 대부분의 케이스에서 시간초과가 발생했다.  
조금 더 획기적인 방식으로 문제를 풀어야 할 것 같다.  

### DP
곰곰히 생각을 해보니 DP와 유사하다는 생각이 들었다.  
그래서 DP의 ```Memoization```을 이용해보기로 결심했다.  
어떻게 점화식을 세울 수 있을까?  

우선 나는 DP 배열의 각 칸을 점수로 두기로 했다.  
즉, ```DP[100001]```의 배열을 만들어 계산을 진행하고자 한다.  

진행 방식은 점수로 부터 0까지 내려가며 ```for```문을 돌리려고 한다.  
아래와 같은 ```for```문을 사용할 수 있을 것이다.  
{% highlight cpp %}
for (int curr_score = target; curr_score > 0; curr_score--){
  // 점수 계산
}
{% endhighlight %}

이제 다음으로는 점수를 어떻게 계산해 나갈 것인가가 관건이다.  
내가 맞출 수 있는 점수는 ```1 ~ 20```과 각각의 ```X2, X3```그리고 ```50```점이다.  
해당 점수들을 얻었을 때 ```target - score```에 해당하는 칸에 횟수를 기록할 것이다.  

단, 이 때 위에서 DFS를 진행할 때 살펴보았던 중지 조건과 동일한 조건 하에서  
더 이상 탐색할 필요가 없을 때는, 해당 회차를 중지시켜야 한다.  
아래와 같은 검사 함수를 만들어 줄 수 있을 것이다.  

{% highlight cpp %}
bool validate(int next_score, int curr_score) {
    if (next_score < 0)
        return false; // 버스트

    if (dp[next_score].first && (dp[next_score].first <= dp[curr_score].first))
        return false;
    // 다음 점수에 이미 탐색한 기록이 있는데
    // 횟수가 현재 횟수와 같거나 더 적으면 탐색할 필요 X

    if ((dp[next_score].first == dp[curr_score].first + 1) && dp[next_score].second > dp[curr_score].second)
        return false;
    // 현재 점수에서 하나 더 던져서 다음 점수를 만들 수 있는 경우
    // 즉, 던진 횟수가 같은 tie break의 경우에
    // 볼 + 싱글의 경우가 현재 더 적으면 탐색할 필요 X

    return true;
}
{% endhighlight %}  

이제는 우리가 원하는 점수로 부터 내려가며, 배열에 차곡차곡 쌓으면 된다!  
아래와 같이 최종적으록 구현되었다.  
{% highlight cpp %}
vector<int> answer;
for (int curr_score = target; curr_score > 0; curr_score--) {
    for (int next_score = 1; next_score <= 20; next_score++) {
        for (int coef = 1; coef <= 3; coef++) {
            int next = curr_score - (next_score * coef);

            if (!validate(next, curr_score))
                continue;

            dp[next].first = dp[curr_score].first + 1;
            if (coef == 1) // 싱글을 맞추는 경우
                dp[next].second = dp[curr_score].second + 1;
            else // 아닌 경우 현행 유지
                dp[next].second = dp[curr_score].second;
        }
    }

    int next = curr_score - 50; // 불
        
    if (!validate(next, curr_score))
        continue;

    dp[next].first = dp[curr_score].first + 1;
    dp[next].second = dp[curr_score].second + 1;
    // 불은 무조건 올려주어야 함
}

answer.push_back(dp[0].first);
answer.push_back(dp[0].second);
return answer;
{% endhighlight %}  

### Appendix : DP 튜닝
다른 사람들의 풀이를 참고하기 위해 찾아보니, 다양한 풀이가 있었다.  
그런데 대부분 DP를 그냥 사용하는 것이 아니라 분기를 하고 있었다.  
내가 생각하기엔 조금 수학적인 부분들이 많이 가미된 풀이인 것 같다.  

먼저 생각해야 할 조건들 부터 생각해보자.  
1. 대상 점수가 1 ~ 20 사이일 경우
  - 이는 바로 싱글 1회에 끝낼 수 있다. ```[1, 1]```
2. 대상 점수가 21 ~ 40 사이일 경우
  - 2의 배수라면 더블 1회에 끝낼 수 있다. ```[1, 0]```
3. 대상 점수가 21 ~ 60 사이일 경우
  - 3의 배수라면 트리플 1회에 끝낼 수 있다. ```[1, 0]```
4. 50인 경우
  - 불 1회에 끝낼 수 있다. ```[1, 1]```

위의 경우들은 모두 다트를 한 번 던져 낼 수 있는 점수이다.  
그럼 이후 ```61점``` 이상의 점수들부터는 어떻게 계산할 수 있을까?  
그 이후로는 ```50 ~ 70점``` 구간으로 한 번 더 잘라 생각해야 한다.  
1. 대상 점수가 ```50 ~ 70점``` 사이인 경우
  - 이 경우는 반드시 ```불 + 싱글```의 경우가 최선이다. ```[2, 2]```
2. 대상 점수가 ```70점``` 이하인데, ```2, 3```의 배수가 아닌 경우
  - 이 경우는 ```더블, 트리플``` 한 번에 끝낼 수 없는 경우이다.
  - 다시 두 가지 경우로 분리할 수 있다.
2-1. ```40점``` 이하에서 ```2, 3```의 배수가 아닌 경우
  - 이 경우엔, ```싱글 + 싱글```의 경우가 최선이다. ```[2, 1]```
2-2. 나머지 경우
  - 이 경우엔, ```싱글 + 더블``` 혹은 ```싱글 + 트리플```이 최선이다. ```[2, 1]```

이제 진짜 남은 모든 경우인 ```70점``` 이상의 경우는 어떻게 할까?  
이후로도 분명 많은 조건에 따라 자를 수 있겠지만,  
```Memoization```을 통해 계산해도 충분하다고 생각된다.  

