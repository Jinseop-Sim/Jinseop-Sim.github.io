---
layout: post
title: "프로그래머스 Level 2 - 숫자 블록"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [숫자 블록[LEVEL 2]](https://school.programmers.co.kr/learn/courses/30/lessons/12923)  

### 1차 시도 : Brute Force
처음 시도는 단순히 이중 포문을 통한 완전 탐색이었다.  
n의 배수인 블록의 값을 n으로 교체하는 방식으로 진행했다.  
하지만 당연하게도 시간초과가 발생해서 다른 방법을 고안했다.  

### 2차 시도 : 가장 큰 약수
이번에 시도한 방식은 바깥의 while문 제거가 가능해서 획기적이라고 생각했다.  

- Begin부터 End까지 순회를 하며 각 칸에 들어갈 값은?
  - 해당 번호를 나누어 떨어트릴 수 있는 가장 큰 수!
  - 그럼 가장 큰 약수를 구할 수 있는 함수를 따로 만들어서 이용하자.  

하지만 이 방법 또한 효율성 측면에서 모두 시간초과가 발생했다.  
다른 방법을 또 고안을 해보았다.  

{% highlight cpp %}
#include <string>
#include <vector>
using namespace std;

long long get_divisor(long long num) {
    long long key = num / 2;

    while (true) {
        if ((num % key) == 0)
            return key;

        key--;
    }
}

void pave_road(vector<int>& answer, long long begin, long long end) {
    long long limit = 2;

    for (long long i = begin; i <= end; i++) {
        if (i == 1) continue;
        answer[i - begin] = get_divisor(i);
    }
}

vector<int> solution(long long begin, long long end) {
    long long road_length = (end - begin) + 1;
    vector<int> answer(road_length, 1);
    answer[0] = 0; // Init Zero block

    pave_road(answer, begin, end);
    return answer;
}
{% endhighlight %}

### 3차 시도 : 문제 조건의 재발견
가장 먼저, 가장 큰 약수를 구하는 Logic을 바꾸었다.  

- 2부터 해당 수의 제곱근까지 반복문을 돌린다.  
  - 그럼 가장 작은 약수가 제일 먼저 찾아질 것이다.
  - 그렇다면 (해당 수 / 가장 작은 약수) = 가장 큰 약수가 된다!

하지만 위의 Logic만으로는 통과할 수가 없었다.  
그래서 게시판의 질문들을 살펴본 결과, 수가 너무 큰 것이 문제였다.  
만약 최대 약수가 10,000,000 보다 커버리면? 그 블록은 쓸 수 없게 된다.  
따라서 그 다음으로 큰 약수를 저장을 해주어야 한다는 것이 핵심 조건!  

{% highlight cpp %}
#include <string>
#include <vector>
#include <cmath>
using namespace std;
#define BIGINT 10000000
long long get_divisor(long long num) {
    for (long long i = 2; i <= sqrt(num); i++) {
        if (num % i == 0 && (num / i) <= BIGINT)
            return (num / i);
    }

    return 1;
}

void pave_road(vector<int>& answer, long long begin, long long end) {
    long long limit = 2;

    for (long long i = begin; i <= end; i++) {
        if (i == 1) continue;
        answer[i - begin] = get_divisor(i);
    }
}

vector<int> solution(long long begin, long long end) {
    long long road_length = (end - begin) + 1;
    vector<int> answer(road_length, 1);
    answer[0] = 0; // Init Zero block

    pave_road(answer, begin, end);
    return answer;
}
{% endhighlight %}