---
layout: post
title: "Tree�� Graph�� ����"
categories: Algorithm
tags: [cpp]
author:
  - Jinseop Sim
math: true
---

�˰��� ������ Ǯ�ٺ��� �츮�� Graph�� Tree�� �̿��ϴ� ��츦 ���� ������ �ȴ�.  
Tree�� Graph�� � ������� ������ �� �ִ� �� �����غ���.  

## Tree ������ ����
### Binary Tree
#### �迭 �̿��ؼ� �����ϱ�  

���� Ʈ���� Ư�� �� Depth�� $$n$$�϶� $$2^(n-1)$$�� ��带 ������.  
�̷� Ư���� �̿��ؼ� �츮�� �ܼ� �迭�� ���� ���� Ʈ�� ������ �����ϴ�.   

�迭�� �̿��ؼ� �����ϴ� ��쿡�� ���� ����Ʈ���̰ų�,  
��ȭ ����Ʈ���� ��쿡 ����Ǵ� ������ ��� �����ϴ�!  

- Example : ���� 5639 - ���� Ž�� Ʈ��

���� Ž�� Ʈ���� ��� ����Ʈ���� ���� �׻� �Ʒ� ������ �����ϴ� Ʈ���̴�.  
- ���� ��尡 �θ𺸴� ���� ��.  
- ���� ��尡 �θ𺸴� Ŭ ��.  

�Ʒ��� ���� �ܼ� �迭�� �̿��ؼ� ���� Ž�� Ʈ���� ������ �� �ִ�.  

{% highlight cpp %}
#include <iostream>
using namespace std;
#define MAX 100001

int bst[MAX] = { 0, };
bool is_root = true;

void make_bst(int pos, int num) {
	if (is_root) { // Root node�� �Է� �޴� ���
		bst[pos] = num;
		is_root = false;
	}
	else {
		if (bst[pos] > num) { // �θ𺸴� ���� ���
			if (bst[pos * 2] == 0) bst[pos * 2] = num;
			// ���� ��尡 ������� ����.
			else make_bst(pos * 2, num);
			// ���� ��尡 ���� �ʾ����� �� ��忡 ���� ��� Ž��
		}
		else { // �θ𺸴� ū ���
			if (bst[pos * 2 + 1] == 0) bst[pos * 2 + 1] = num;
			// ���� ��尡 ������� ����.
			else make_bst(pos * 2 + 1, num);
			// ���� ��尡 ���� �ʾ����� �� ��忡 ���� ��� Ž��
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

������ �̷��� ������ �ϴ� ��쿣 Tree�� ���̰� ��� ���� ���� �𸣱� ������,  
�迭�� ���̸� �츮�� ���� �����ϱⰡ �ſ� ��ٷο��  
�ð��� ���� ��� �԰� �ȴ�.  

#### ������ �̿��ؼ� �����ϱ�

�׷��� ������ �Ʒ��� ���� �����͸� �̿��Ѵ�.  

{% highlight cpp %}

{% endhighlight %}

## Graph ������ ����
### Direct Graph

