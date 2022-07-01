---
layout: post
title: "Linked List : Circular"
categories: Algorithm
tags: [cpp]
author:
  - Jinseop Sim
---

## Linked List : 원형 연결 리스트

연결 리스트 중에서도, 꼬리가 머리를 가리키고 있는 원형 리스트이다.  
구조는 아래와 같다.  

![image](https://user-images.githubusercontent.com/71700079/176916723-af637648-df60-4469-ac99-bf8e7ee74c52.png) 

### Example : 백준 1158 : 요세푸스 문제
{% highlight cpp %}
#include <iostream>
using namespace std;
#define NEXT_NODE cursor = cursor->next

struct Node{
    int _id;
    Node* next;
    Node(int _id, Node* next) {
        this->_id = _id;
        this->next = next;
    }
}; // 연결 리스트 구조체의 선언.

int n = 0, k = 0;
void init_list(Node*& cursor, Node* head) {
    for (int i = 1; i < n + 1; i++) {
        if (i == n) {
            cursor->next = head;
            NEXT_NODE; break;
        }
        cursor->next = new Node(i + 1, NULL);
        NEXT_NODE;
    }
} // 사람 수 대로 Node 초기화

void josephus(Node*& cursor, int k, bool is_end) {
    Node* prevNode = NULL;
    
    if (k == 1)
        prevNode = cursor;
    if (k == 2)
        prevNode = cursor->next;
    // 이 조건들을 넣어 주지 않으면
    // prevNode가 nullptr를 참조한다.

    for (int i = 0; i < k; i++) {
        if (i == k-2)
            prevNode = cursor;
        if (i == k-1) {
            if (!is_end) cout << cursor->_id << ", ";
            else cout << cursor->_id;
            prevNode->next = cursor->next;
            NEXT_NODE; break;
        }
        NEXT_NODE;
    }
} // 순서대로 죽일 사람 정하기

int main() {
    cin >> n >> k;

    int cnt = n; bool is_end = false;
    Node* head = new Node(1, NULL);
    Node* cursor = head;

    init_list(cursor, head);

    cout << "<";
    while (cnt--) {
        if (cnt == 0) is_end = true;
        josephus(cursor, k, is_end);
    }
    cout << ">";

    return 0;
}
{% endhighlight %}
