---
layout: post
title: "프로그래머스 Level 3 - 최고의 집합"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
  math: true
---
- [최고의 집합[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/12938)  

### 1차 시도 : Backtracking
처음엔 n개의 수로 합이 s가 되는 조합을 모두 구해서 곱을 계산시켰다.  
하지만 우려했던대로, 당연하게도 시간초과가 발생했다.  

### 2차 시도 : 수학적 접근
도저히 생각이 나질 않아서 다른 분들의 풀이를 참고했다.  
굉장히 어이가 없는 풀이였다. 핵심은 아래와 같다.  

- 곱이 최대가 되는 경우는 최대한 균등하게 수가 분배되었을 때이다.  

이 문장을 차근차근 증명해보자.  
$$a + b = C$$(C는 상수)라고 가정해보자.  
$$a + (C - a) = C$$ 라고 둬도 같은 식이다.  
그럼 결국 우리가 구하는 것은 $$(a(C - a)) = -a^2 - Ca$$ 라는 이차 함수가 된다.  
해당 이차함수는 아래 그림과 같은 사진임을 알 수 있다.  

따라서, 최대가 되는 a값은 C/2가 된다.  
하지만 C가 홀수일 경우엔 C/2가 실수이므로, ```{C-1, C}```가 될 때 최대가 될 것이다.  

위의 원리에 따라 코드를 짜면 아래와 같다.  
{% highlight cpp %}
#include <string>
#include <vector>

using namespace std;

vector<int> solution(int n, int s)
{
    vector<int> answer;

    if (n > s) {
        answer.push_back(-1);
        return answer;
    }

    for(int i = 1; i <= n; i++)
        answer.push_back(s / n);
    
    for(int i = 1; i <= s % n; i++)
        answer[n - i]++;

    return answer;
}
{% endhighlight %}