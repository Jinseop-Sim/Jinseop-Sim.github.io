---
layout: post
title: "프로그래머스 Level 2 - 조이스틱"
categories: Programmers
tags: [cpp]
author:
  - Jinseop Sim
---

## 프로그래머스 Level 2 : 조이스틱

생각보다 많이 까다로운 Greedy 문제였다.  
![image](https://user-images.githubusercontent.com/71700079/175919129-c4f347bb-1d46-4640-bab9-2dd9336c91e8.png)  

### 1차 시도 (48점)  

처음엔 아래와 같이 Code를 짰다.  
하지만 이 Code에는 문제점이 몇 가지 있었다.  
1. A가 아닌 다음 Index를 검색할 때, 배열의 맨 끝으로 이동할 수 없다.  
2. 앞으로만 검색을 하고 있다.

{% highlight cpp %}
#include <iostream>
using namespace std;

pair<int, int> idxs = {0, 0};

int calc_alpha(string name) {
	int cnt = 0;
	for (int i = 0; i < name.size(); i++)
		if (name[i] != 'A') cnt++;
	return cnt;
}

pair<int, int> search_next(string& name, int idx) {
	for (int i = idx; i < name.size(); i++)
		if (name[i] != 'A')
			return make_pair(idx, i);
}

int calc_distance(string name) {
	int size_v = name.size();
	return min(idxs.second - idxs.first, idxs.first + (size_v - idxs.second));
}

int up_down(char part_name) {
	if (part_name - 'A' < 14)
		return part_name - 'A';
	else return 26 - ((part_name) -'A');
}

int solution(string name) {
	int answer = 0;
	int cnt = calc_alpha(name);

	while (true) {
		if (cnt == 0) break;
		idxs = search_next(name, idxs.second);
		answer += up_down(name[idxs.second]);
		name[idxs.second] = 'A';
		answer += calc_distance(name);
		cnt--;
	}

	return answer;
}

int main() {
	string name;
	cin >> name;

	cout << solution(name);
	return 0;
}
{% endhighlight %}

### 2차 시도

도저히 생각이 나지 않아 다른 사람들의 힘을 빌렸다.  
세상엔 참 똑똑한 사람이 많은 것 같다.  

{% highlight cpp %}
#include <string>
using namespace std;

int up_down(char part_name) {
	if (part_name - 'A' < 14)
		return part_name - 'A';
	else return 26 - ((part_name) -'A');
}

int solution(string name) {
	int answer = 0;
    int turn = name.size()-1; // 커서가 한 쪽 방향으로만 가는 경우
    
    for(int i = 0; i<name.size(); i++){
        answer += up_down(name[i]);
        int index = i+1; // 다음 바꿀 index
        while(index < name.size() && name[index] == 'A')
            index++;
        
        int a = i; // 원점 ~ 현재 index
        int b = name.size() - index; // 배열 끝 ~ 다음 바꿀 index
        turn = min(turn, min(2*a+b, a+2*b));
    }
    
    answer += turn;
	return answer;
}
{% endhighlight %}
