---
layout: post
title: "Divide & Conquer"
categories: Algorithm
math: true
toc: true
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
피보나치 수열은 $$F(n) = F(n-1) + F(n-2)$$를 점화식으로 갖는다.  
매우 유명한 점화식이기 때문에, 많은 사람들이 이미 알고있으며,  
피보나치 수열을 풀 때 재귀 뿐만 아니라 DP로도 풀 수 있음이 이미 자명하다.  

해당 점화식에 Memoization을 어떻게 적용할 수 있을까?  
바로 배열에 $$F(n)$$ 값을 저장하며 앞으로 채워나가는 것이다.  
반복문을 돌며 ```f[n] = f[n-1] + f[n-2]```와 같이 채워나간다면?  
이전 단계에서 저장된 ```f[n-1], f[n-2]```가 다음 계산에 사용되게 된다.  

이와 같은 방식을 동적 계획법의 Memoization이라고 한다.  

### 분할 정복의 Memoization?
분할 정복은 소문제의 결과를 어딘가에 저장하거나 하지 않는다.  
배열과 같은 메모리를 이용하지 않고, 재귀 함수를 통해 곧바로 결과를 이어받는다.  
즉, Memoization을 이용하지 않고 별개의 결과들을 모아 큰 문제를 푸는 것이다.  

또한, 쪼개진 소문제가 중복되지 않는다고 하였는데 다시 피보나치를 생각해보자.  
피보나치는 $$F(6) = F(5) + F(4)$$이고, $$F(7) = F(6) + F(5)$$ 이기 때문에  
소문제 $$F(5)$$가 중복되어 사용됨을 한 눈에 바로 확인할 수 있다.  
소문제가 중복되는 경우는 이런 경우를 의미하는 것이다.  
이런 경우에는, Memoization을 통해 저장된 결과를 이용하면 빠른 연산이 가능하다.  
하지만 분할 정복은 이런 경우에 사용하지 않는다는 말이다.  
아래로 대표적인 분할 정복 문제 예시들을 보자.  

## 분할 정복 대표 문제
### 쿼드 트리
첫번째 대표 문제는 쿼드 트리(Quad tree)이다.  
이는 주로 이미지를 비트맵으로 압축하는 데에 사용되는 방식의 알고리즘이다.  
전체 이미지를 __픽셀 단위까지 분할하여 압축__ 표현하는 방식이며,  
픽셀 단위까지 분할할 때에 분할 정복법이 사용된다.  
문제 예시 코드는 아래와 같다.  

> 백준 1992 : 쿼드 트리  
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
분할 정복법의 대표격 문제인 하노이의 탑이다.  
하노이의 탑 문제는 어릴 때 부터 많이 본 문제일 것이다.  
하지만 정확한 풀이나 내용을 알고 있지는 않았는데, 이번에 알게되었다.  
자세한 풀이 방식은 아래와 같다.

하노이의 탑은 3개의 기둥 사이에서 N개의 원판을 옮기는 문제이다.  
이 문제에 분할 정복법을 사용할 수 있는 이유는, 원판을 옮기는 규칙 때문이다.  
아래에 깔린 원판은 먼저 옮길 수 없으며, 위에서부터 차례로 원판을 옮겨야한다.  
따라서 문제의 순서를 아래와 같이 생각해보자.  

1. N개의 원판 중 N-1개를 2번 기둥으로 옮긴다.
2. 남은 하나의 큰 원판을 3번 기둥으로 옮긴다.
3. N-1개의 기둥을 3번 기둥으로 마저 옮긴다.

위와 같이 문제를 푸는 단계를 간략화 할 수 있다.  
이 때, N-1개의 기둥을 옮기는 과정은 또 다시 위의 과정을 반복하는 것이다.  
N-1, N-2, N-3, ... 마지막 원판 1개가 남을 때 까지 파고들어 진행하게 된다.  
따라서 이 문제는 1개, 2개, 3개 .. N-1개의 원판을 옮기는 단계가 결합된,  
분할 정복 문제로 분류할 수 있는 것이다.  

이 문제는 위의 단계들에 의해 $$F(N) = F(N-1) + 1 F(N-1)$$의 점화식을 가진다.  
이는 $$F(N) = 2F(N-1) + 1$$로 축약되며, 모든 단계에 대해 점화식을 세워보면,  
$$F(N-1) = 2F(N-2) + 1, F(N-2) = 2F(N-3) + 1$$ ... 와 같이 반복되며,  
해당 식은 $$F(N) + 1 = 2{F(N-2) + 1}$$로 변형할 수가 있다.  
위의 형태로 점화식들의 양변을 모두 곱해보면 아래와 같은 결과가 나오게 된다.  

$${F(N)+1}{F(N-1)+1}...{F(2)+1} = 2^(N-1){F(N-1)+1}{F(N-2)+1)}..{F(1)+1}$$  
위의 식에서 양변의 공통 인수들을 나누게 되면, 아래의 형태가 된다.  

$${F(N) + 1} = 2^(N-1){F(1) + 1}$$  
이 때, $$F(1)$$은 원판 하나만 있는 경우이기 때문에, 1이 되므로 최종식은,  
$$F(N) = 2^(N-1) - 1$$ 이라는 결과가 나오게 된다.  
해당 결과는 N개의 원판을 모두 옮기는 데에는 $$2^(N-1)$$회의 움직임이 필요하다는 것이다.  
아래는 하노이 탑 문제에 대한 코드이다.  

> 백준 1914 : 하노이 탑  
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

> 백준 1629 : 곱셈  
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
