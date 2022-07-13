---
layout: post
title: "프로그래머스 Level 3 - GPS"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [이중 우선 순위 큐[LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/42628)

### 1차 시도 : BST(Binary Search Tree)

힙을 사용해서 푸는 문제인 것 같길래 처음엔 이진 트리를 구현해보려 했다.  
하지만 Node를 삭제하는 부분에서 고난을 겪고 노선을 틀었다.  
이진 트리의 구현에 대해서 더 공부를 해야할 것 같다.  

{% highlight cpp %}
#include <string>
#include <vector>
#include <iostream>
using namespace std;

struct Node {
    int data;
    Node* left;
    Node* right;
    Node(int data) {
        this->data = data;
        this->left = NULL;
        this->right = NULL;
    }
};

Node* root = new Node(NULL);

int convert(string target) {
    string temp = "";
    for (int i = 2; i < target.size(); i++)
        temp += target[i];

    return stoi(temp);
}

Node* tree_insert(Node* node, int data) {
    if (root->data == NULL) {
        root->data = data;
        return node;
    }
    else {
        if (node == NULL)
            node = new Node(data);
        else {
            if (data < node->data)
                node->left = tree_insert(node->left, data);
            else
                node->right = tree_insert(node->right, data);
        }
    }

    return node;
}

Node* find_max(Node* node) {
    if (node->right != NULL)
        find_max(node->right);
    else
        return node;
}

Node* find_min(Node* node) {
    if (node->left != NULL)
        find_min(node->left);
    else
        return node;
}

void tree_delete(int cmd) {
    if (root == NULL) return;

    if (cmd == 1) *find_max(root) = NULL;
    if (cmd == -1) *find_min(root) = NULL;
}

vector<int> solution(vector<string> operations) {
    vector<int> answer;

    for (int i = 0; i < operations.size(); i++) {
        switch (operations[i][0])
        {
        case 'I':
            tree_insert(root, convert(operations[i]));
            break;
        case 'D':
            tree_delete(convert(operations[i]));
            break;
        }
    }

    answer.push_back(find_max(root)->data);
    answer.push_back(find_min(root)->data);
    return answer;
}

{% endhighlight %}


### 2차 시도 : Set 이용

2차 시도에서는 이진 균형 트리의 Container인 Set을 이용했다.  
Set의 최대 최소를 찾아서 지우고, 반환했다.  

{% highlight cpp %}
#include <string>
#include <vector>
#include <set>
using namespace std;

set<int> balanced_tree;

int convert(string target) {
    string temp = "";
    for (int i = 2; i < target.size(); i++)
        temp += target[i];

    return stoi(temp);
}

void delete_element(int cmd) {
    if (balanced_tree.empty()) return;

    int max_v = *balanced_tree.begin();
    int min_v = *(--balanced_tree.end());
    if (cmd == 1) balanced_tree.erase(min_v);
    if (cmd == -1) balanced_tree.erase(max_v);
}

vector<int> solution(vector<string> operations) {
    vector<int> answer;

    for (int i = 0; i < operations.size(); i++) {
        switch (operations[i][0])
        {
        case 'I':
            balanced_tree.insert(convert(operations[i]));
            break;
        case 'D':
            delete_element(convert(operations[i]));
            break;
        }
    }

    if (balanced_tree.empty()) {
        answer.push_back(0);
        answer.push_back(0);
    }
    else {
        answer.push_back(*(--balanced_tree.end()));
        answer.push_back(*balanced_tree.begin());
    }
    return answer;
}
{% endhighlight %}