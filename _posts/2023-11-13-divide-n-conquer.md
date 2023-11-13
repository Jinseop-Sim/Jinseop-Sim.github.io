---
layout: post
title: "Divide & Conquer"
categories: Algorithm
tags: [cpp]
author:
  - Jinseop Sim
---
__Divide & Conquer,__ 분할 정복 알고리즘에 대해 공부해보자.  

먼저 __분할 정복법이란__ 무엇일까?  
간단히 말하면, 크고 복잡한 문제를 작은 소문제로 쪼개어 문제를 풀어 나가는 것이다.  
어디선가 많이 들어본 말인 것 같다.  

바로 __동적 계획법(DP)과__ 굉장히 유사한 개념임을 알 수 있다.  
하지만 분할 정복법은 동적 계획법과는 아래와 같은 차이가 존재한다.  

|-|동적 계획법|분할 정복법|
|:--:|:--:|:--:|
|공통점|큰 문제를 작은 문제로|쪼개어 풀어나간다.|
|차이점|Memoization이 사용된다.|Memoization이 사용되지 않는다.|
|-|쪼개진 소문제가 중복된다.|쪼개진 소문제는 중복되지 않는다.|

### Memoization이란?
Memoization이란, 동적 계획법의 핵심이자 꽃이다.  
이전 단계의 결과를 기억하여 다음 계산에 사용하는 것을 의미한다.  

피보나치 수열을 예시로 들어서 생각해보자.  
피보나치 수열은 ```f(n) = f(n-1) + f(n-2)```를 점화식으로 갖는다.  
매우 유명한 점화식이기 때문에, 많은 사람들이 이미 알고있으며,  
피보나치 수열을 풀 때 재귀 뿐만 아니라 DP로도 풀 수 있음이 이미 자명하다.  

해당 점화식에 Memoization을 어떻게 적용할 수 있을까?  
바로 배열에 ```f(n)``` 값을 저장하며 앞으로 채워나가는 것이다.  
반복문을 돌며 ```f[n] = f[n-1] + f[n-2]```와 같이 채워나간다면?  
이전 단계에서 저장된 ```f[n-1], f[n-2]```가 다음 계산에 사용되게 된다.  

이와 같은 방식을 동적 계획법의 Memoization이라고 한다.  

### 분할 정복의 Memoization?
분할 정복은 소문제의 결과를 어딘가에 저장하거나 하지 않는다.  
배열과 같은 메모리를 이용하지 않고, 재귀 함수를 통해 곧바로 결과를 이어받는다.

## 분할 정복 대표 문제
### 쿼드 트리
{%highlight cpp%}
void compress_quad_tree(int x_idx, int y_idx, int len) {
	if (len == 1) {
		// 길이가 1까지 왔다면 그냥 출력을 하면 된다.
		cout << quad_tree[x_idx][y_idx];
		return;
	}

	pair<bool, bool> flag_pair = check_compression(x_idx, y_idx, len);
  // 0이나 1로 압축이 가능한지 검사하는 함수
  
	if (flag_pair.first) // 모두 0인 경우
		cout << 0;
	else if (flag_pair.second)
		cout << 1; // 모두 1인 경우
	else { // 0과 1이 섞여있는 경우
		cout << "(";
		// 쿼드트리의 기본, 순서를 반드시 맞춰주어야 함.
		compress_quad_tree(x_idx, y_idx, len/2); // 왼쪽 위
		compress_quad_tree(x_idx, y_idx + len/2, len/2); // 오른쪽 위
		compress_quad_tree(x_idx + len/2, y_idx, len/2); // 왼쪽 아래
		compress_quad_tree(x_idx + len/2, y_idx + len/2, len/2); // 오른쪽 아래
		cout << ")";
	}

	return;
}
{%endhighlight%}  

### 하노이의 탑
{%highlight cpp%}
void move_hanoi(ll depth, int source, int auxiliary, int destination) {
	// 하노이의 탑은 분할 정복 알고리즘의 대표적인 문제이다.
	if (depth == 1) {
		cout << source << " " << destination << "\n";
		return;
	}

	// STEP 1. n-1개의 원판을 보조 기둥으로 옮긴다.
	move_hanoi(depth - 1, source, destination, auxiliary);
	// STEP 2. 남은 1개를 목적지 기둥으로 옮긴다.
	cout << source << " " << destination << "\n";
	// STEP 3. 보조 기둥에 옮겼던 n-1개의 원판을 목적지 기둥으로 옮긴다.
	move_hanoi(depth - 1, auxiliary, source, destination);
}
{%endhighlight%}  

### 거듭 제곱
{%highlight cpp%}
ll power(ll mult) {
	// 분할 정복의 기초, 쪼개어 큰 문제를 해결한다.
	// 거듭제곱은? b승이 짝수 -> a^(b/2) * a^(b/2)
	// b승이 홀수 -> a^(b/2) * a^(b/2 + 1)
	if (mult == 0)
		return 1; // 0승은 1이다.
	if (mult == 1)
		return num % divisor; // 1승은 그냥 나머지

	remaind = power(mult / 2) % divisor;
  // 이 부분이 핵심이다.
  // 계산량을 줄이기 위해, remaind 변수에서 나머지를 계속 기억하고 있는다.
  
	if (mult % 2 == 0)
		return (remaind * remaind) % divisor;
  // 기억하고 있던 나머지끼리 곱하고 다시 나누어 결과를 계산한다.
	else
		return (((remaind * remaind) % divisor) * num) % divisor;
  // mult승이 홀수 승인 경우에는, 뒤에 num을 한번 더 곱한 뒤 나누어준다.
  // 이 방식은 O(logN)이 걸리는 분할 정복법을 통한 거듭제곱 방식이다.
}
{%endhighlight%}
