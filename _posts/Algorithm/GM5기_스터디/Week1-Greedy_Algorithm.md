---
title: "Week1 - Greedy Algorithm"
date: 2021-08-11 03:32:00 +0900
categories: [Algorithm, GM5기_스터디]
tags: [baekjoon, cpp, greedy, study]
---

## **📝 문제 목록**
- [🥉1] [2839 - 설탕 배달](https://www.acmicpc.net/problem/2839)
- [🥈4] [2217 - 로프](https://www.acmicpc.net/problem/2217)
- ~~[🥇5] [1461 - 도서관](https://www.acmicpc.net/problem/1461)~~ (통과 X)

---

## **1. 설탕 배달**
백준 로그를 보니 작년 이맘때쯤 한번 풀었던 문제였다.
우선 5kg짜리로만 만들 수 있는 최대 개수 res를 구한 후
res>0이고 나머지가 3kg로 나누어 떨어질때까지 계속 5kg짜리를 하나씩 빼 주었다.
5kg 개수가 0이 될 동안 한번도 3kg로 나눌 수 없었다면 -1를, 아니면 총 봉지 개수를 구해 출력하게 했다.
난이도도 어렵지 않고 한번 풀었던 문제라 그런지 풀이는 금방 생각났다.

### 코드
```cpp
#include<iostream>

using namespace std;

int main() {
	int n, res;
	
	cin >> n;
	res = n / 5;
	
	while ((res > 0) && ((n - res * 5) % 3) != 0) res--;

	cout << (((n - res * 5) % 3 != 0) ? -1 : res + (n - res * 5) / 3);
}
```
<br>

## **2. 로프**
이 문제 풀면서 썼던 메모가 어딨는지 모르겠다😑💦 뭔가 고민했던 것 같은데...
일단 약한 로프는 무거운 물체를 들면 끊어지기 쉬우므로 별 도움이 되지 않으니, 나중에 고려하도록 했다.
즉 로프를 튼튼한 순으로(=내림차순) 정렬했다.
그리고 순서대로 로프를 포함시킨다고 가정했을 때 들 수 있는 최대 중량을 계산하여, 현재까지 구해진 최대 중량보다 많아지면 최대 중량값을 갱신했다.
(최대 중량은 사용하는 로프들 중 가장 약한 로프를 기준으로 거기에 전체 로프개수를 곱한 값이다. 가장 약한 로프가 끊어지면 안 되니까.)

### 코드
```cpp
#include<iostream>
#include<vector>
#include<algorithm>

using namespace std;

int main(){
	int n, cnt;
	int max=0;
	cin>>n;

	vector<int> rope(n,0);
	
	for(int i=0; i<n; i++){
		cin>>rope[i];
	}
	
	sort(rope.begin(), rope.end(), greater<int>());
	
	for(cnt=1; cnt<=n; cnt++){
		if(rope[cnt-1]*cnt>max) max=rope[cnt-1]*cnt;
	}
	
	cout<<max;
}
```
<br>

## **~~3. 도서관~~ (통과 X)**
풀이 생각해내는데도 시간이 좀 걸렸는데, 마지막에 실수한 탓에 통과하지 못했다.
어디가 잘못됐는지 한참을 못 찾아서 주위에 물어봤는데 알고나니 너무나 허무한...이걸 왜 못봤지?ㅋㅋ
한달 뒤에 다시 풀어보고 풀이 작성해야겠다.
