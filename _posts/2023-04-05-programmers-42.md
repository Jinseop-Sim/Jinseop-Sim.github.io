---
layout: post
title: "프로그래머스 Level 3 - 억억단을 외우자"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [억억단을 외우자[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/138475)  

위의 문제는 약수의 개수 구하기가 핵심인 문제이다.  
시간 복잡도에 굉장히 민감한 문제였기 때문에,  
어떻게 시간을 줄일지 많은 고민이 필요했다.  

그래서 이렇게 글로 정리하며 고민해보기로 결심했다.  

### 간단하지만 복잡한, O(N^2)
간단히, 아주 간단히 생각 했을 때 O(N^2)의 복잡도로 구할 수 있다.  
2중 for문을 사용하는 코드이기 때문에, O(N^2)의 복잡도를 가진다.  

약수를 이렇게 무작정 구하니, 당연히 시간초과가 발생했다.  

[코드 첨부]

### 같은 N(O^2), 다른 느낌
약수를 구하는 로직을 조금 변경했다.   
에라토스테네스의 체를 거꾸로 하는 느낌의 로직이다.  
앞으로 나아가며 해당 수의 배수 위치에만 +1을 해준다.  

하지만 이 로직 또한 시간초과가 발생했다.  
약수를 구하는 것이 아닌, 최대값을 구하는 것이 문제였던 것이다.  

[코드 첨부]

### 문제는 Index
결국 문제는 최대값이 위치한 Index를 구하는 방식이었다.  
원래는 C++의 내장 함수인 ```max_element()```를 사용했었다.  
하지만 해당 함수 자체로 O(N)의 복잡도를 가지기 때문에,  
for문 내에서 돌아가게 되면 또 O(N^2)의 복잡도를 가지게 되어 시간초과가 난다.  

따라서, 방법을 바꾸어 O(N)만에 최대 index를 구해낼 수 있도록 한다.  
뒤에서 부터 배열을 훑으며 최대 값을 저장하며 나아가는 방식이다.  

{% highlight cpp %}
vector<int> solution(int e, vector<int> starts) {
    vector<int> answer;
    vector<int> divisors(e + 1);
    vector<int> maxvec(e + 1);
    int max = 0, max_idx = 0;

    for (int i = 1; i <= e; i++) {
        for (int j = i; j <= e; j += i) {
            divisors[j] += 1;
        }
    }

    
    for (int i = e; i >= 0; i--) {
        max_idx = max > divisors[i] ? max_idx : i;
        max = max > divisors[i] ? max : divisors[i];
        maxvec[i] = max_idx;
    }

    for (auto i : starts)
        answer.push_back(maxvec[i]);

    return answer;
}
{% endhighlight %}
