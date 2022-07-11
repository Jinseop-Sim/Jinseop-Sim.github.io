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

