---
layout: post
title: "Two Pointer"
categories: Algorithm
tags: [cpp]
author:
  - Jinseop Sim
---
> 특정 조건을 만족하는 구간을 구하고 싶을 때 이용하는 알고리즘이다.  

### Example : 백준 2003 - 수들의 합 2

{% highlight cpp %}
#include <iostream>
using namespace std;
#define MAX 100001

int n = 0, m = 0, temp = 0, cnt = 0;
int arr[MAX] = { 0, };

void two_pointer() {
    int start = 0, end = 0, sum = 0;

    while (end != n) {
        while (sum < m) {
            sum += arr[end];
            end++;
            if (sum == m) cnt++;
            if (end == n) break;
        }

        sum -= arr[start];
        start++;
        if (sum == m) cnt++;
        if (end == n) break;
    }
}

int main() {
    cin >> n >> m;
    for (int i = 0; i < n; i++) {
        cin >> temp;
        arr[i] = temp;
    }

    two_pointer();
    cout << cnt;
    return 0;
}
{% endhighlight %}