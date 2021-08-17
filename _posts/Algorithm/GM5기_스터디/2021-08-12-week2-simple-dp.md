---
title: "Week2 - Simple DP"
date: 2021-08-12 03:00:00 +0900
categories: [Algorithm, GM5기_스터디]
tags: [baekjoon, cpp, DP, study]
---

## **📝 문제 목록**
- [🥉1] [2748 - 피보나치 수 2](https://www.acmicpc.net/problem/2748)
- [🥈3] [11727 - 2×n 타일링 2](https://www.acmicpc.net/problem/11727)
- [🥈1] [1149 - RGB거리](https://www.acmicpc.net/problem/1149)
- ~~[🥇4] [17404 - RGB거리 2](https://www.acmicpc.net/problem/17404)~~ (통과 X)

---
<br>

## **1. 피보나치 수 2**
재귀를 이용하면 시간초과가 나므로 메모이제이션을 사용해야 한다.  
바로 풀긴 했는데 범위...이런 실수는 하지 말자!  
참 글 쓰니까 생각났는데 굳이 n개 배열을 만들 필요 없이 3개만으로 갱신해가며 풀어도 된다.  

### 코드
```cpp
#include<iostream>
#include<vector>

using namespace std;

int main() {
	int n;
	vector<long long> fib;
	cin >> n;

	fib.assign(n+1, 0);
	fib[1] = 1;

	for (int i = 2; i <= n; i++) {
		fib[i] = fib[i - 1] + fib[i - 2];
	}

	cout << fib[n];
}
```
<br>

## **2. 2×n 타일링 2**
이 문제 한 5번은 풀지 않았을까?ㅋㅋ그래서 풀이는 바로 떠올랐다.  
2×n 직사각형을 채우는 방법의 수 = 2×(n-1) 직사각형을 채우는 수 + 2×(n-2) 직사각형을 채우는 수*2 이다.  
왜냐면 2×(n-1) 직사각형에서 '1×2짜리 하나'를 채운 것이거나  
2×(n-1) 직사각형에서 '2×1짜리 두개' or '2×2짜리 하나'를 채운거라고 생각하면 되니까.  
그리고 base case를 n=0일때 1, n=1일때 1로 설정해줬는데, 뭐 결과값은 같지만 '2×0 직사각형'으로 생각하면 혼동이 있을 수 있으므로 n=1을 1로, n=2를 3으로 설정해주는 것도 좋은 것 같다.  
이 문제를 예전에...처음에 풀 때는 2×2칸을 1×2짜리 두개로 채우는 방법 때문이었는지 아무튼 삽질을 좀 했던 기억이 난다.  

### 코드
```cpp
#include<iostream>

using namespace std;

int main() {
	int n;
	int cnt[1001] = { 0 };
	cin >> n;

	cnt[0] = 1;
	cnt[1] = 1;

	for (int i = 2; i <= n; i++) {
		cnt[i] = (cnt[i - 1] + cnt[i - 2] * 2) % 10007;
	}

	cout << cnt[n];
}
```
<br>

## **3. RGB거리**
보자마자 프로그래머스의 [땅따먹기](https://programmers.co.kr/learn/courses/30/lessons/12913) 문제가 생각났다. 거의 똑같은 문제인데... 당시에 머리를 싸매고 풀었지만 도저히 생각이 안 나서 결국 풀이를 봤더랬다.  
사실은 이번에도 바로 떠올리진 못했고..ㅎ 내가 왜 어려워했었는지 되짚어보다가 풀이가 생각났다.  
처음에 풀 때 어려움을 느꼈던 이유가 'n번째 줄에서 어떤 인덱스를 선택했느냐에 따라 그 이전 선택이 바뀔수도 있다(영향을 준다)' 라고 생각했기 때문인데,  
이전 줄에서 현재 인덱스를 제외한 나머지 중 베스트 값을 구해서 현재 인덱스값에 누적해놓으면, 어차피 인덱스가 서로 겹치지 않으므로 더 이상 이전을 신경쓸 필요가 없어지게 된다. (설명하기가 좀 어려운 것 같다🥲)  
아무튼 그렇게 풀어서 통과는 했는데 코드가 아무래도 보기 쉽지 않아서 다른 스터디원 분들이 짠 방식으로 고쳤다.  

### 코드
```cpp
#include<iostream>

using namespace std;

int min(int a, int b) {
	return ((a < b) ? a : b);
}

int main() {
	int n;
	int stacked_cost[3] = { 0 };
	cin >> n;

	for (int i = 0; i < n; i++) {
		int cur_cost[3];

		for (int j = 0; j < 3; j++) {
			cin >> cur_cost[j];

			int prev_min_cost = INT32_MAX;
			for (int k = 0; k < 3; k++) {
				if ((j != k) && (prev_min_cost > stacked_cost[k])) prev_min_cost = stacked_cost[k];
			}

			cur_cost[j] += prev_min_cost;
		}

		for (int j = 0; j < 3; j++) {
			stacked_cost[j] = cur_cost[j];
		}
	}

	cout << min(stacked_cost[0], min(stacked_cost[1], stacked_cost[2]));
}
```
### 코드 #2
```cpp
#include<iostream>

using namespace std;

int min(int a, int b) {
	return (a < b) ? a : b;
}

int main() {
	int n;
	int cost[1001][3];
	cin >> n;

	for (int i = 0; i < 3; i++) {
		cin >> cost[1][i];
	}

	for (int i = 2; i <= n; i++) {
		for (int j = 0; j < 3; j++) {
			cin >> cost[i][j];
		}
		cost[i][0] += min(cost[i - 1][1], cost[i - 1][2]);
		cost[i][1] += min(cost[i - 1][0], cost[i - 1][2]);
		cost[i][2] += min(cost[i - 1][0], cost[i - 1][1]);
	}

	cout << min(cost[n][0], min(cost[n][1], cost[n][2]));
}
```
<br>

## **~~3. RGB거리~~ (통과 X)**
한참을 생각했으나... 풀이를 생각해내지 못했다.  
위 문제에서 첫줄을 제외하고 비용을 구한 다음 두번째 줄에 선택한 인덱스와 마지막줄 선택 인덱스를 제외시키고 비교하는... 뭐 그런 방법을 생각하고 짰는데. 아무래도 석연찮기는 했다.  
근데 예제랑 질문글에 올라온 반례 두어 개가 다 잘 나와서, 시간이 걸리더라도 웬만하면 풀이 보지 말고 고쳐보자 했는데  
고치고 제출해도 계속 실패가 뜨니 내가 확실히 놓친 부분이 있는 것 같아 결국 풀이를 봤다. 아니 이런 방법이.ㅋㅋㅋ OTL  
한달 뒤에 다시 풀어보고 풀이 작성해야겠다.  