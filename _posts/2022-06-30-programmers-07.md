---
layout: post
title: "프로그래머스 Level 2 - 예상 대진표"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---

## 프로그래머스 Level 2 : 예상 대진표

![image](https://user-images.githubusercontent.com/71700079/176624176-598f8ac0-0dc6-4892-99e6-6220e62724f1.png)    

처음엔 간단한 이진탐색 문제라고 생각하고 접근했다.  

### 1차 시도 : 이진 탐색

이진탐색을 통해서 반씩 잘라나가면서 계산하도록 했다.  
하지만 재귀가 너무 많이 도는지, Segmentation Fault가 발생했다.  

{% highlight cpp %}
int binary_search(int n, int a, int b) {
    if (a <= n / 2 && b > n / 2) {
        return log2(n);
    }
    else if (a > n / 2 && b > n / 2) {
        binary_search(n / 2, a - n / 2, b - n / 2);
    }
    else if (a <= n / 2 && b <= n / 2) {
        binary_search(n / 2, a, b);
    }
}

int solution(int n, int a, int b)
{
    int answer = 0;
    
    answer = binary_search(n, a, b);
    return answer;
}

{% endhighlight %}

### 2차 시도

이진 탐색으로는 Seg Fault가 발생해서, 접근법을 바꿨다.  
주어진 n으로 계산하는 것이 아닌, a와 b로 계산을 하도록 했다.  

a와 b가 같아질 때 까지 Loop를 돌아, 만나는 Round를 계산한다.  

{% highlight cpp %}
int solution(int n, int a, int b)
{
    int answer = 0;
    while (a != b) {
        a = (a + 1) / 2;
        b = (b + 1) / 2;
        answer++;
    }
    return answer;
}

int main() {
    cin >> n >> a >> b;
    cout << solution(n, a, b);
    return 0;
}
{% endhighlight %}
