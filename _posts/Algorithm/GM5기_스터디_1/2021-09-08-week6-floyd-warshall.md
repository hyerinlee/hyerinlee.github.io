---
title: "Week6 - Floyd Warshall"
date: 2021-09-08 20:05:00 +0900
categories: [Algorithm, GM5기_스터디_1]
tags: [baekjoon, cpp, floyd_warshall, study]
---

## **📝 문제 목록**
- [🥇4] [11404 - 플로이드](https://www.acmicpc.net/problem/11404)
- [🥇5] [2660 - 회장뽑기](https://www.acmicpc.net/problem/2660)
- [🥇5] [15723 - n단 논법](https://www.acmicpc.net/problem/15723)

---

> 개인적으로 공부한 내용을 작성한 것으로 틀린 부분이 있을 수 있습니다  
틀린 부분이 있다면 지적해주시면 감사하겠습니다🙏  

<br>

## **1. 플로이드**
문제 제목부터 아예 플로이드라고 적혀있는만큼 플로이드 워셜 알고리즘으로 풀 수 있는 대표적인 유형의 문제이다.  
모든 정점의 쌍(A,B)에 대해 A에서 B로 가는 최소비용을 모두 구하는 문제.  
사실 플로이드 워셜 알고리즘을 이번에 처음 접했는지 아니면 오래되어 까먹은 건지 기억이 안 나서;; 이 문제는 공부하는 겸 알고리즘 설명을 보면서 구현했다.  
이 알고리즘의 시간복잡도는  
```
모든 정점 i에 대하여 => O(V)
i를 거치는 모든 경로(A->B)에 대해 비용을 비교 => O(V^2)
```
이므로 총 O(V^3)이 된다.  

이 문제에서 코드를 작성하면서 다음과 같은 점을 주의했다.  
1. 입력에서 주어지는 값 중 A->B로 가는 노선이 여러개일 수 있으므로 입력받을 때도 최소값으로 갱신해주어야 함
2. 무한(INF) 값을 충분히 크게 해 주어야 함:  
갈 수 없는 경로에 대하여 INF 값을 정해줄 때 처음에는 단순히 비용의 최대값보다 1 큰 100,001로 설정해 주었는데, 돌아서 가는 경우를 고려하면 최소한 (비용의 최대값*(도시의 최대 개수-1)+1) 이상으로 설정해 주어야 한다.
3. 출발점과 도착점이 같을 경우 0으로 초기화:  
A->A 경로가 없다고 하더라도 이 경우에는 INF로 초기화해주면 안된다. 아래 코드같은 경우에는 출발점과 도착점이 같은 경우에 대해 따로 넘겨주지 않기 때문에 i를 거치는 경로로 갱신될 수 있기 때문이다.  

물론 2, 3번은 `갈 수 없는 경로`나 `출발점=도착점` 인 경우에 대해 플로이드 워셜 알고리즘에서 패스하도록 코드를 작성했다면 굳이 신경쓰지 않아도 될 부분이다.  
나는 아래 코드와 같이 3중 for문이 간결한 방식으로 작성해서 그렇지만, 만약 for문에서 패스하도록 조건문을 넣어주었다면  
for문을 도는 시간이 줄어드므로 약간 속도를 높일 수도 있을 것이고,  
갈 수 없는 경로와 출발점=도착점을 모두 0으로 초기화해놓았다면 INF도 필요 없고 이후 0으로 고쳐 출력할 필요도 없다.  

### 코드
```cpp
#include<iostream>

using namespace std;
const int MAX = 9900000;

int min(int a, int b) {
	return (a < b) ? a : b;
}

int main() {
	ios_base::sync_with_stdio(false);
	cin.tie(NULL);
	cout.tie(NULL);

	int n, m;
	int cost[101][101];
	cin >> n >> m;

	fill_n(cost[0], 101 * 101, MAX + 1);
	for (int i = 1; i <= n; i++) {
		cost[i][i] = 0;
	}

	for (int i = 0; i < m; i++) {
		int from;
		int to;
		int c;
		cin >> from >> to >> c;
		cost[from][to] = min(cost[from][to], c);
	}

	for (int i = 1; i <= n; i++) {
		for (int from = 1; from <= n; from++) {
			for (int to = 1; to <= n; to++) {
				if (cost[from][to] > cost[from][i] + cost[i][to])
					cost[from][to] = cost[from][i] + cost[i][to];
			}
		}
	}

	for (int from = 1; from <= n; from++) {
		for (int to = 1; to <= n; to++) {
			// 갈 수 없을 경우 0으로 표시
			cout << ((cost[from][to] > MAX) ? 0 : cost[from][to]) << " ";
		}
		cout << "\n";
	}
}
```
<br>

## **2. 회장뽑기**
문제 설명 때문에 좀 헷갈릴 수 있지만 이전 문제와 결국 똑같다.  
(솔직히 모르고 봤으면 플로이드 워셜 알고리즘을 떠올리지 못했을 것 같기도 하다=_=;;)  
여기서 말하는 회장후보란 다른 모든 회원들과 제일 사이가 가까운 사람, 즉 다른 모든 정점과 거리가 가장 가까운 정점이라는 뜻이다.  
예를 들어 어떤 모임의 회장 후보의 가장 먼 친구사이가 '친구의 친구' 라면 회장 후보의 점수는 2이다.  

이전 문제와 같이 모든 회원들과의 최소 거리를 저장하기 위해 int형 2차원 배열을 사용했고,  
모든 회원에 대하여 가장 먼 사이의 값을 구해서 오름차순 정렬해야 하므로 `회원의 인덱스`와 `가장 먼 사이 값`을 저장하는 pair형 배열도 사용했다.  
(pair형 배열의 정렬을 위해 second 값으로 비교하는 compare 함수를 만들어 주고 sort()에서 사용하였다.)  
그리고 이후에 회장 후보의 수와 후보를 차례대로 출력하기 위해 int형 queue도 사용했다.  

### 코드
```cpp
#include<iostream>
#include<algorithm>
#include<queue>

using namespace std;

bool compare(pair<int, int> a, pair<int, int> b) {
	if (a.second == b.second) return a.first < b.first;
	else return a.second < b.second;
}

int max(int a, int b) {
	return (a > b) ? a : b;
}

int main() {
	ios_base::sync_with_stdio(false);
	cin.tie(NULL);
	cout.tie(NULL);

	int n;
	int score[51][51];
	pair<int, int> max_score[51];
	queue<int> candidate;
	cin >> n;

	fill_n(score[0], 51 * 51, n - 1);
	for (int i = 1; i <= n; i++) {
		score[i][i] = 0;
		max_score[i] = { i,0 };
	}

	while (true) {
		int a, b;
		cin >> a >> b;
		if (a == -1) break;
		score[a][b] = 1;
		score[b][a] = 1;
	}

	// from~to의 최단경로 각각 구하기
	for (int i = 1; i <= n; i++) {
		for (int from = 1; from <= n; from++) {
			for (int to = 1; to <= n; to++) {
				if (score[from][to] > score[from][i] + score[i][to])
					score[from][to] = score[from][i] + score[i][to];
			}
		}
	}

	// 각 정점에 대해 다른 모든 정점과의 거리 중 가장 먼 값 구하기
	for (int i = 1; i <= n; i++) {
		for (int j = 1; j <= n; j++) {
			max_score[i].second = max(max_score[i].second, score[i][j]);
		}
	}

	// 점수 오름차순으로 정렬
	sort(max_score + 1, max_score + n + 1, compare);

	// 회장 후보만 큐에 넣음
	candidate.push(max_score[1].first);
	for (int i = 2; i <= n; i++) {
		if (max_score[1].second == max_score[i].second) candidate.push(max_score[i].first);
		else break;
	}

	cout << max_score[1].second << " " << candidate.size() << "\n";
	while (!candidate.empty()) {
		cout << candidate.front() << " ";
		candidate.pop();
	}
}
```
<br>

## **3. n단 논법**

> A→B이고 B→C이면 A→C이다.

위의 삼단논법을 응용해 전제와 결론이 주어지면 결론이 참인지 거짓인지를 판별하는 문제이다.  
간선 비용이 따로 없으므로 여기서는 bool형 2차원 배열을 사용해 일치 관계(true/false)만을 저장하도록 했다.  

알고리즘은 이전 문제와 다를 것 없고 단순하지만 배열에 데이터를 저장하기 위해 입력받은 문자를 int로 변환하는 작업이 필요하다.  
이때 나는 전제 문장을 getline()으로 한번에 받아서 처음과 끝을 뽑는 방식으로 작성했기 때문에 n, m을 입력받은 후에 개행문자를 지워주기 위해(flushing) cin.ignore()를 사용했는데  
스터디원분에게 받은 피드백에 의하면 그냥 간단하게 중간의 "is"를 string으로 받아버려도 된다.  

### 코드
```cpp
#include<iostream>
#include<string>

using namespace std;

int main() {
	ios_base::sync_with_stdio(false);
	cin.tie(NULL);
	cout.tie(NULL);

	int n, m;
	bool equal[26][26];
	char result[10];
	cin >> n;
	cin.ignore(32767, '\n');

	fill_n(equal[0], 26 * 26, false);

	// 전제 입력받기
	for (int i = 0; i < n; i++) {
		string premise;
		getline(cin, premise);
		int first = premise.front() - 'a';
		int second = premise.back() - 'a';
		equal[first][second] = true;
		equal[first][first] = true;
		equal[second][second] = true;
	}

	// n단 논법
	for (int i = 0; i < 26; i++) {
		for (int first = 0; first < 26; first++) {
			for (int second = 0; second < 26; second++) {
				if (equal[first][i] && equal[i][second]) {
					equal[first][second] = true;
				}
			}
		}
	}

	cin >> m;
	cin.ignore(32767, '\n');

	for (int i = 0; i < m; i++) {
		string premise;
		getline(cin, premise);
		int first = premise.front() - 'a';
		int second = premise.back() - 'a';
		result[i] = (equal[first][second]) ? 'T' : 'F';
	}

	for (int i = 0; i < m; i++) {
		cout << result[i] << "\n";
	}
}
```
