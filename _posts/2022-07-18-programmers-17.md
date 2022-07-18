---
layout: post
title: "프로그래머스 Level 2 - 2개 이하로 다른 비트 "
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---

- [2개 이하로 다른 비트[LEVEL 2]](https://school.programmers.co.kr/learn/courses/30/lessons/77885)

### 1차 시도 : 완전 탐색

처음엔 수의 최대 값을 $$2^15$$라고 보고 10진수를 2진수로 변환해, 완전탐색을 시도했다.  
하지만 수의 최대 값은 $$10^15$$였고, 당연하게도 시간초과가 발생했다.  

{% highlight cpp %}
#include <string>
#include <vector>
#include <algorithm>
using namespace std;

string dec_to_bin(long long src) {
    string temp = "";
    int iter = 0;

    while (true) {
        temp += to_string(src % 2);
        if (src == 0) break;
        src /= 2; iter++;
    }
    
    reverse(temp.begin(), temp.end());
    return temp;
}

long long compare_bit(long long src, string bin_src) {
    string target = "";
    while (true) {
        int diff = 0;
        src++;
        target = dec_to_bin(src);

        if (target.size() > bin_src.size()) {
            reverse(bin_src.begin(), bin_src.end());
            for (int i = 0; i < (target.size() - bin_src.size()); i++)
                bin_src += '0';
            reverse(bin_src.begin(), bin_src.end());
        }

        int cmp_size = target.size() - 1;
        for (int i = 0; i < target.size(); i++)
            if (bin_src[i] != target[i]) diff++;

        if (diff <= 2) break;
    }

    return src;
}

vector<long long> solution(vector<long long> numbers) {
    vector<long long> answer;

    for (int i = 0; i < numbers.size(); i++) {
        string bin_src;
        bin_src = dec_to_bin(numbers[i]);
        answer.push_back(compare_bit(numbers[i], bin_src));
    }

    return answer;
}
{% endhighlight %}

### 2차 시도 : 규칙성

결국 구글에 검색을 해서 참고를 했다.  

이 문제에는 규칙이 존재했다.  
1. 짝수일 경우엔, ```값 + 1``` 이 반드시 답이다.  
2. 홀수의 경우에는, 최하위 bit부터 탐색해 처음 0이 나오는 비트 바로 아랫자리에 1을 더해주면 정답이 된다.  

정말 대단한 아이디어다.   

{% highlight cpp %}
#include <iostream>
#include <string>
#include <vector>
using namespace std;

vector<long long> solution(vector<long long> numbers) {
    vector<long long> answer;

    for (auto num : numbers) {
        if (num % 2 == 0) answer.push_back(num + 1);
        else {
            long long key = 1;

            while (true) {
                if ((num & key) == 0) break;
                key *= 2;
            }
            key /= 2;

            answer.push_back(num + key);
        }
    }

    return answer;
}
{% endhighlight %}