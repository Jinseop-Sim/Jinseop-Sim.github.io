---
layout: post
title: "프로그래머스 Level 3 - 표 편집"
categories: [KAKAO] Programmers
tags: [cpp]
author:
  - Jinseop Sim
---
- [표 편집[LEVEL 3]](https://programmers.co.kr/learn/courses/30/lessons/81303)  

2021 카카오 채용연계형 인턴십 코딩 테스트 문제였다.  
처음엔 단순히 String과 Stack을 이용해서 풀려고 시도했다.  

### 1차 시도 : String과 Stack, For문

아래와 같이 문제를 푸는 경우엔, 정확성 검사는 다 맞는다.  
하지만 효율성이 매우 떨어지는 로직이므로, 시간 초과가 발생한다.  

{% highlight cpp %}
#include <vector>
#include <stack>
#include <string>
using namespace std;

stack<int> ctrlz;
string make_base(int n, string answer) {
    for (int i = 0; i < n; i++)
        answer += 'O';

    return answer;
}

int read_count(string cmd) {
    string temp;
    for (int i = 2; i < cmd.size(); i++) {
        temp += cmd[i];
    }

    return stoi(temp);
}

string solution(int n, int k, vector<string> cmd) {
    string answer = "";
    int cursor = k;
    answer = make_base(n, answer);

    for (int i = 0; i < cmd.size(); i++) {
        int idx = 0;
        bool is_last = true;
        switch (cmd[i][0]) {
        case 'U':
            for (int curr = cursor - 1; curr >= 0; curr--) {
                if (answer[curr] == 'X') continue;
                idx++;
                if (idx == (read_count(cmd[i]))) {
                    cursor = curr; break;
                }
            }
            cout << cursor << endl;
            break;
        case 'D':
            for (int curr = cursor+1; curr < answer.size(); curr++) {
                if (answer[curr] == 'X') continue;
                idx++;
                if (idx == (read_count(cmd[i]))) {
                    cursor = curr; break;
                }
            }
            cout << cursor << endl;
            break;
        case 'C':
            answer[cursor] = 'X';
            ctrlz.push(cursor);
            for (int curr = cursor+1; curr < answer.size(); curr++) {
                if (answer[curr] == 'O') {
                    cursor = curr;
                    is_last = false;
                    break;
                }
            }
            if (is_last) {
                for (int curr = cursor-1; curr >= 0; curr--) {
                    if (answer[curr] == 'O') {
                        cursor = curr;
                        break;
                    }
                }
            }
            break;
        case 'Z':
            answer[ctrlz.top()] = 'O';
            ctrlz.pop();
            break;
        }
    }

    return answer;
}
{% endhighlight %}

### 2차 시도 : 연결 리스트

아무리 생각해보아도 풀이가 떠오르지 않아서 검색해보니  
정석적인 풀이는 연결 리스트로 표를 만드는 것이라고 한다.  

{% highlight cpp %}
#include <vector>
#include <stack>
#include <string>
using namespace std;

struct Node { // Linked List 구조 생성
    int n;
    Node* prev;
    Node* next;
    Node(int n, Node* prev, Node* next) {
        this->n = n;
        this->prev = prev;
        this->next = next;
    } // Constructor
};

stack<Node*> ctrlz;
string make_base(int n, string answer) {
    for (int i = 0; i < n; i++)
        answer += 'O';

    return answer;
}

int convert(string cmd) {
    string temp;
    for (int i = 2; i < cmd.size(); i++) {
        temp += cmd[i];
    } // 몇 칸 이동하는지 변환

    return stoi(temp);
}

string solution(int n, int k, vector<string> cmd) {
    string answer = "";
    answer = make_base(n, answer);

    Node* cursor = new Node(0, NULL, NULL);
    for (int i = 1; i < n; i++) {
        cursor->next = new Node(i, cursor, NULL);
        cursor = cursor->next;
    } // 0 ~ n-1 만큼 node 초기화

    for (int i = 0; i < n - k - 1; i++) cursor = cursor->prev;
    // k 위치로 cursor를 옮김.

    for (string comm : cmd) {
        int curr = 0;
        switch (comm[0]) {
        case 'U':
            curr = read_count(comm);
            while (curr--) // Curr이 0이 아닌 동안.
                cursor = cursor->prev; // 위치 이동
            break;
        case 'D':
            curr = read_count(comm);
            while (curr--)
                cursor = cursor->next;
            break;
        case 'C':
            ctrlz.push(cursor);
            answer[cursor->n] = 'X';
            if (cursor->prev != NULL) cursor->prev->next = cursor->next;
            // 이전 node가 존재하면, 이전 node를 지울 node의 다음 node와 연결!
            if (cursor->next != NULL) cursor->next->prev = cursor->prev;
            // 다음 node가 존재하면, 다음 node를 지울 node의 이전 node와 연결!
            if (cursor->next == NULL) cursor = cursor->prev;
            // 다음 node가 없다면, 그냥 이전 node로 커서를 옮긴다.
            else cursor = cursor->next;
            // 이전 node가 없다면, 그냥 다음 node로 커서를 옮긴다.
            break;
        case 'Z':
            Node * undo = ctrlz.top();
            answer[undo->n] = 'O';
            ctrlz.pop();
            if (undo->prev != NULL) undo->prev->next = undo;
            // 아까 삭제했던 node의 이전 node가 존재한다면, 다시 이어준다.
            if (undo->next != NULL) undo->next->prev = undo;
            // 아까 삭제했던 node의 다음 node가 존재한다면, 또 이어준다.
            break;
        }
    }

    return answer;
}
{% endhighlight %}

Linked List에 대한 공부가 더 필요할 것 같다.  