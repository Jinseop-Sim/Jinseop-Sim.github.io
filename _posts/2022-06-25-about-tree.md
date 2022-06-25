---
layout: post
title: "Tree와 Graph의 구현"
categories: Algorithm
tags: [cpp]
author:
  - Jinseop Sim
math: true
---

알고리즘 문제를 풀다보면 우리는 Graph나 Tree를 이용하는 경우를 자주 만나게 된다.  
Tree와 Graph를 어떤 방식으로 구현할 수 있는 지 정리해보자.  

## Tree 구조의 구현
### Binary Tree
#### 배열 이용해서 구현하기  

이진 트리는 특성 상 Depth가 $$n$$일때 $$2^(n-1)$$의 노드를 가진다.  
이런 특성을 이용해서 우리는 단순 배열을 통해 이진 트리 구현이 가능하다.   

배열을 이용해서 구현하는 경우에는 완전 이진트리이거나,  
포화 이진트리의 경우에 낭비되는 공간이 없어서 유용하다!  

- Example : 백준 5639 - 이진 탐색 트리

이진 탐색 트리는 모든 서브트리에 대해 항상 아래 조건을 만족하는 트리이다.  
- 좌측 노드가 부모보다 작을 것.  
- 우측 노드가 부모보다 클 것.  

아래와 같이 단순 배열을 이용해서 이진 탐색 트리를 구현할 수 있다.  

{% highlight cpp %}
#include <iostream>
using namespace std;
#define MAX 100001

int bst[MAX] = { 0, };
bool is_root = true;

void make_bst(int pos, int num) {
	if (is_root) { // Root node를 입력 받는 경우
		bst[pos] = num;
		is_root = false;
	}
	else {
		if (bst[pos] > num) { // 부모보다 작은 경우
			if (bst[pos * 2] == 0) bst[pos * 2] = num;
			// 좌측 노드가 비었으면 입장.
			else make_bst(pos * 2, num);
			// 좌측 노드가 비지 않았으면 그 노드에 대해 재귀 탐색
		}
		else { // 부모보다 큰 경우
			if (bst[pos * 2 + 1] == 0) bst[pos * 2 + 1] = num;
			// 우측 노드가 비었으면 입장.
			else make_bst(pos * 2 + 1, num);
			// 우측 노드가 비지 않았으면 그 노드에 대해 재귀 탐색
		}
	}
}

void postorder(int curr) {
	if (curr > 100000) return;

	postorder(curr * 2);
	postorder(curr * 2 + 1);
	if(bst[curr] != 0) cout << bst[curr] << '\n';
}

int main() {
	int num = 0;
	while (cin >> num) {
		if (num == cin.eof()) break;
		make_bst(1, num);
	}

	postorder(1);

	return 0;
}
{% endhighlight %}

하지만 이렇게 구현을 하는 경우엔 Tree의 깊이가 어느 정도 될지 모르기 때문에,  
배열의 길이를 우리가 직접 설정하기가 매우 까다로우며  
시간도 많이 잡아 먹게 된다.  

#### 포인터 이용해서 구현하기

그래서 보통은 아래와 같이 포인터를 이용한다.  

{% highlight cpp %}

{% endhighlight %}

## Graph 구조의 구현
### Direct Graph

