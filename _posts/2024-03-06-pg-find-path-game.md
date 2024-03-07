---
layout: post
title: "프로그래머스 Level 3 - 길 찾기 게임"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [길 찾기 게임 [LEVEL 3]](https://school.programmers.co.kr/learn/courses/30/lessons/42892)

이번 문제는 프로그래머스의 레벨 3 길 찾기 게임 문제이다.  
2019 KAKAO 공채 코딩테스트에 출제 되었던 문제라고 한다.  
겁 먹지 말고 문제를 차근차근 읽어보도록 하자.  

이진트리를 구성할 노드가 좌표로써 주어진다.  
최대 ```10,000```개의 좌표가 주어지며, 각 순서는 노드의 번호를 의미한다.  
또한 각 좌표의 ```x, y```는 최대 ```100,000``` 까지 주어질 수 있다.  
또한 트리의 깊이는 ```1,000```이하인 경우만 주어진다.  
이 때, 해당 트리의 전위, 후위 순회 결과를 반환하라!  

### 이진 트리 : 1차원 배열
사실 이 문제는 이진트리를 어떻게 구성하는가가 관건이다.  

처음에는 배열로 이진트리를 구성할 수 있을까 생각해보았다.  
1차원 배열을 선언해, 1번을 root로 삼고 순서대로 채우는 것이다.  
2번이 root의 왼쪽 자식, 3번이 root의 오른쪽 자식... 이런 방식으로 채워 나간다.  

이 때 중요한 점은 좌표가 순서대로 정렬이 되어야 한다는 점이다.  
단, ```y``` 좌표 우선 정렬, ```x``` 좌표 차선 정렬이어야 한다.  
그 이유는 ```x```가 ```좌, 우```를 결정하고 ```y```가 ```깊이```를 결정하기 때문이다.  
정렬된 채로 앞에서 부터 훑으면 트리를 구성할 수 있지 않을까?  

하지만 이 방법에는 큰 결함이 있었다.  
우리는 좌표를 통해 왼쪽, 오른쪽 자식을 결정할 수 있어야 한다.  
즉, 부모의 x좌표와 현재 노드의 x좌표를 비교할 수 있어야 한다.  
이를 단순 배열로 연산하기에는 무리가 있어 보인다.  

### 이진 트리 : 구조체
두 번째로 생각난 방식은 구조체로 트리 구조를 만드는 것이다.  
보통은 이 방식으로 트리 구조를 선언해서 사용한다.  

구조체에 ```x, y, idx, left, right```를 넣어 직접 트리 구조를 만들자.  
아래와 같이 구조체를 선언했다.  
{% highlight cpp %}
struct Node {
    int x;
    int idx;
    Node* left_child;
    Node* right_child;

    Node(int x, int idx, Node* left_child, Node* right_child) {
        this->x = x;
        this->idx = idx;
        this->left_child = left_child;
        this->right_child = right_child;
    }
};
{% endhighlight %}  

이제 ```root```부터 탐색하며 차례차례 아래로 노드를 채워 트리를 구성한다.  
트리에 노드를 삽입하는 동작은 아래와 같이 구현하였다.  
{% highlight cpp %}
void insert_node(Node *curr_node, Node *new_node) {
    if (curr_node->x <= new_node->x) {
    // 현재 노드의 x값이 새 노드보다 작은 경우
        if (curr_node->right_child == NULL)
            curr_node->right_child = new_node;
        // 현재 노드의 오른쪽 자식이 비었으면 추가
        else if (curr_node->right_child != NULL)
            insert_node(curr_node->right_child, new_node);
        // 현재 노드의 오른쪽 자식이 비지 않았으면 이동
    }
    else {
    // 현재 노드의 x값이 새 노드보다 큰 경우
        if (curr_node->left_child == NULL)
            curr_node->left_child = new_node;
        else if (curr_node->left_child != NULL)
            insert_node(curr_node->left_child, new_node);
    }
}
{% endhighlight %}  

구조체에 저장된 ```x```좌표를 통해 이진트리를 구성할 수 있다.  
현재 노드의 좌표와 대상 노드의 좌표를 비교하여 ```좌, 우```를 정한다.  
이제 아래와 같이 노드를 삽입하여 트리를 구성하도록 한다.  
{% highlight cpp %}
Node *root = new Node(node_info[0].second.first, node_info[0].first, NULL, NULL);
    for (int i = 1; i < node_info.size(); i++)
        insert_node(root, new Node(node_info[i].second.first, node_info[i].first, NULL, NULL));
{% endhighlight %}  

이 때, 배열 트리를 시도할 때와 마찬가지로 좌표는 정렬되어야 한다.  
```y```좌표 우선 기준, ```x```좌표 차선 기준으로 정렬을 하자.  
이후 마지막으로, ```전위 순회, 후위 순회```만 돌아주면 끝이다.  
최종적으로 아래와 같이 구현되었다.  
{% highlight cpp %}
vector<int> inorder_vec;
vector<int> postorder_vec;
struct Node {
    int x;
    int idx;
    Node* left_child;
    Node* right_child;

    Node(int x, int idx, Node* left_child, Node* right_child) {
        this->x = x;
        this->idx = idx;
        this->left_child = left_child;
        this->right_child = right_child;
    }
};

bool cmp(piii a, piii b) {
    if (a.second.second == b.second.second)
        return a.second.first < b.second.first;
    return a.second.second > b.second.second;
    // Y축 우선 정렬, 이후 X축 차선 정렬
}

void insert_node(Node *curr_node, Node *new_node) {
    if (curr_node->x <= new_node->x) {
    // 현재 노드의 x값이 새 노드보다 작은 경우
        if (curr_node->right_child == NULL)
            curr_node->right_child = new_node;
        // 현재 노드의 오른쪽 자식이 비었으면 추가
        else if (curr_node->right_child != NULL)
            insert_node(curr_node->right_child, new_node);
        // 현재 노드의 오른쪽 자식이 비지 않았으면 이동
    }
    else {
    // 현재 노드의 x값이 새 노드보다 큰 경우
        if (curr_node->left_child == NULL)
            curr_node->left_child = new_node;
        else if (curr_node->left_child != NULL)
            insert_node(curr_node->left_child, new_node);
    }
}

void inorder(Node *curr_node) {
    inorder_vec.push_back(curr_node->idx);

    if(curr_node->left_child != NULL)
        inorder(curr_node->left_child);
    if(curr_node->right_child != NULL)
        inorder(curr_node->right_child);
}

void postorder(Node* curr_node) {
    if (curr_node->left_child != NULL)
        postorder(curr_node->left_child);
    if (curr_node->right_child != NULL)
        postorder(curr_node->right_child);

    postorder_vec.push_back(curr_node->idx);
}

vector<vector<int>> solution(vector<vector<int>> nodeinfo) {
    vector<vector<int>> answer;

    vector<piii> node_info;
    for (int i = 1; i <= nodeinfo.size(); i++)
        node_info.push_back({i, {nodeinfo[i - 1][0], nodeinfo[i - 1][1]}});
    // 인덱스, x좌표(좌, 우), y좌표(깊이)
    sort(node_info.begin(), node_info.end(), cmp);

    Node *root = new Node(node_info[0].second.first, node_info[0].first, NULL, NULL);
    for (int i = 1; i < node_info.size(); i++)
        insert_node(root, new Node(node_info[i].second.first, node_info[i].first, NULL, NULL));

    inorder(root);
    answer.push_back(inorder_vec);
    postorder(root);
    answer.push_back(postorder_vec);

    return answer;
}
{% endhighlight %}  

난이도가 그렇게 어렵지는 않았던 문제였다.  
