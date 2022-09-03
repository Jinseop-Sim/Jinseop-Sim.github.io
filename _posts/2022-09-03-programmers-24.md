---
layout: post
title: "프로그래머스 Level 3 - N으로 표현"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [N으로 표현[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/42895)  

난이도 있는 DP(Dynamic Programming) 문제였다.  
아무리 생각해도 도저히 생각이 나지 않아 결국 다른 사람들의 풀이를 참고했다.  

### 왜 DP인가?  
우리가 구해야 하는 것은 최소 몇 개의 N을 써서 number를 만들 수 있는가? 이다.  
그렇다면 1개, 2개, ... X개 까지 한번 직접 써봐야 할 것이다.  

- 1개 : N만 사용 가능하다.
- 2개 : N을 이어 붙인 NN과 (N ? N)이 될 것이다.
  - ?는 사칙연산을 의미한다.
- 3개 : N을 3개 붙인 NNN 또는 (N1 ? N2)가 될 수 있다.  
- 4개 : N을 4개 붙인 NNNN 또는 (N1 ? N3)이나 (N2 ? N2)가 될 수 있다.
- 5개 : N을 5개 붙인 NNNNN 또는 (N1 ? N4)나 (N2 ? N3)이 될 수 있다.  

위 전개를 보았을 때, 이전에 계산해 놓은 값을 이용하는 DP임을 알 수 있다.  

### 구현
먼저, 사칙연산의 결과를 저장하기 위해선 각 N의 수마다 배열이 달려야한다.  
즉, 2차원 배열에 저장을 해야하는데 그러기에는 공간이 매우 커질 것이다.  
따라서 그나마 중복된 값을 제거할 수 있는 __Unordered_Set__ 을 이용하자!  

{% highlight cpp %}
#include <unordered_set>
#include <vector>
using namespace std;

int make_sequence(int N, int idx) {
    // 이 함수는 N, NN, NNN... 을 만들어 내는 함수.
    int seq = N;

    for (int i = 0; i < idx; i++)
        seq = (seq * 10) + N;

    return seq;
}

int fill_dp(int N, int number, vector<unordered_set<int>>& DP) {
    if (N == number) return 1;
    // N이 number과 같으면 무조건 하나면 끝!

    DP[0].insert(N);
    // DP[0]은 N이 1개 필요한 경우를 의미한다.

    for (int i = 1; i < 8; i++) { // DP[8] 부터는 계산할 필요가 없다.
        DP[i].insert(make_sequence(N, i));
        // 위에 구현해놓은 N, NN... Sequence를 삽입한다.

        for (int a = 0; a < i; a++) {
            for (int b = 0; b < i; b++) {
                if (a + b + 1 != i) continue;
        // 이제 2중 for문을 통해 해당 합이 나올 수 있는 경우를 찾자.
        // ex) 1 + 3 = 4, 2 + 2  = 4.
        // +1을 하는 이유는 DP 배열이 0부터 시작했기 때문!

                for (int pv : DP[a]) {
                    for (int nx : DP[b]) {
                // 해당 합에 대한 사칙연산 결과들끼리 다시 연산한다.
                        DP[i].insert(pv + nx);
                        DP[i].insert(pv * nx);

                // 합과 곱은 상관없으나, 차와 나눗셈은 검증이 필요!
                // 음수인 경우엔 저장을 할 필요가 없다.
                // 나눗셈의 경우는 nx가 0이되면 ZeroDivisor 예외 발생!
                        if (pv - nx > 0) DP[i].insert(pv - nx);
                        if (pv / nx > 0 && nx != 0) DP[i].insert(pv / nx);
                    }
                }
            }
        }
        // 해당 칸의 DP내의 Set 안에 number가 존재하면 return!
        // find() 함수가 end()까지 돌았음은 해당 number가 존재하는 idx가 없다는 뜻이다.
        if (DP[i].find(number) != DP[i].end()) return i + 1;
    }
    return -1; // 최소가 8이 넘어가면 -1을 반환한다.
}

int solution(int N, int number) {
    int answer = 0;
    vector<unordered_set<int>> DP(8);

    answer = fill_dp(N, number, DP);

    return answer;
}
{% endhighlight %}