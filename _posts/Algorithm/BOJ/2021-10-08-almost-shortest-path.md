---
title: "5719 : 거의 최단 경로"
date: 2021-10-08 21:00:00 +0900
categories: [Algorithm, BOJ]
tags: [baekjoon, cpp, dijkstra]
---

> 개인적으로 공부한 내용을 작성한 것으로 틀린 부분이 있을 수 있습니다  
틀린 부분이 있다면 지적해주시면 감사하겠습니다🙏  

<br>

## **문제 링크 & 채점 결과**

| 문제 | 문제 제목 | 결과 | 메모리 | 시간 | 언어 | 코드 길이 |
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|<img src="https://d2gd6pc034wcta.cloudfront.net/tier/16.svg" width="12" height="16" vertical-align="middle"> [5719](https://www.acmicpc.net/problem/5719)| 거의 최단 경로 | <span style="color: #009874">**맞았습니다!!**</span> | 2624 <span style="color: #e74c3c">KB</span> | 56 <span style="color: #e74c3c">ms</span> | C++17 | 2100 <span style="color: #e74c3c">B</span> |

<br>

## **문제 리뷰**
스터디에서 다익스트라 알고리즘을 공부하고 나서 추천받은 문제 중 하난데, 플래티넘 문제는 처음이라서 어째 좀 들떴던 것 같다. 티어같은거에 연연하지 말아야 하는데..😑 아무튼 그래선지는 몰라도 문제를 제대로 읽지도 않고 푼 탓에 그만큼 문제푸는 데 시간이 더 걸렸다.  
일단 간단히 문제 설명부터 하자면, 제목에서도 알 수 있듯이 이 문제는 최단경로가 아닌 '거의 최단 경로'를 찾아야 하는 문제다.  
문제에서는 거의 최단 경로에 대해 이렇게 얘기하고 있다.  

>거의 최단 경로란 ***최단 경로에 포함되지 않는 도로로만*** 이루어진 경로 중 가장 짧은 것을 말한다.

즉 거의 최단 경로는 최단경로와 어느 곳도 겹치지 않아야 한다는 말이다.  
그런데 나는 이걸 대충 읽고 넘긴 바람에 그냥 '최단경로를 제외한 나머지 경로 중 가장 짧은 것' 이라고 생각하고 말았다.  
그래서 도착점을 제외한 모든 정점의 최단경로를 구한 다음 도착지 직전 정점들 중 두번째로 가까운 곳을 선택해 출력하게 했다.  
그랬더니 당연하게도 문제에 나온 예제에서부터 오답이 나왔다. 그러나 나는 내 코드의 출력값이 내가 의도한 출력값이었기 때문에ㅋㅋㅋ 예제의 답이 이해가 가질 않았고(하필 또 3개중 2개는 정답이라 더 그랬던 것 같다) 질문 검색 페이지를 둘러봐도 그런 질문을 하는 사람은커녕 질문글 답변에 올라온 반례조차도 틀리는 것이었다.  
그제서야 뭔가 잘못됐다는 걸 느끼고 이유를 이리저리 찾아보다가 문제를 다시 읽어보고는 내가 단단히 잘못 이해하고 있었다는 사실을 깨달았다.  
🤦  
문제를. 잘읽자. 3번 복창했다..  
그리고 애초에 예제의 답부터가 내가 생각한것과 다르면 당연히 바로 문제부터 다시 천천히 읽어봐야 했는데 엉뚱한 짓을 했던 것도 반성했다.  

어쨌든 그래서 다시 문제를 풀어봤다.  
사실은 위 실수 때문에 검색하다가 '최단경로를 삭제한다'는 힌트를 의도치 않게 얻어버렸지만, 그 방법에 대해서는 보지 않았기 때문에 어떻게 최단경로를 삭제할지에 대해 고민해보았다.  
왜냐면 우리는 최단거리가 얼마인가만 알지 그 경로를 다 구해놓지는 않았기 때문이다.  
처음에는 마지막 간선만 끊어버리면?이라는 생각을 잠깐 했지만 곧 아무 차이도 없다는 걸 깨닫고, 전체 간선을 삭제하기 위해 도착점에서 역으로 하나씩 최단거리와 비교하며 삭제해나가는 방법을 생각해냈다.  
그러기 위해서 역방향 간선정보를 저장하는 vector\<int\>형 배열을 하나 만들었고, 처음에 데이터를 입력받을 때 u, v를 반대로 하여 같이 저장해주었다.  
그다음 다익스트라로 최단경로를 구하고, 그 경로를 아래와 같은 방법으로 삭제했다.  
1. int형 큐를 하나 만들어서 도착점을 넣는다.  
2. 큐가 빌 때까지 다음 작업 반복:  
	1)큐에서 정점(to)을 하나 꺼냄  
	2)역방향 그래프를 통해 to의 인접정점(from)을 모두 검사하여 'from까지의 최단경로 + from에서 to까지의 거리 == to까지의 최단경로' 이면 from-to 간선정보 삭제한 후 from을 큐에 넣음. (이때 시작점이면 넣지 않음)  
	
이 작업이 끝나면 최단경로 값을 초기화하고 다시 다익스트라를 수행함으로써 최단경로를 구하도록 했다.  

아래 코드에는 적혀있지 않은데, 최단경로를 지우는 과정에서 같은 정점을 굳이 또 방문할 수 있을 것 같으니 방문체크를 해야 하지 않을까. 그리고 다익스트라 함수 안에 모든 코드를 다 적었는데, 기능별로 함수를 만드는 게 좋을 것 같다.  

### 코드
```cpp
#include <iostream>
#include <vector>
#include <map>
#include <queue>

using namespace std;
const int INF = 99999999;

int Dijkstra(int n, int m) {
	int s, d;	// 정점 수, 간선 수, 시작점, 도착점
	int dist[501];	// 최단거리(도착점 제외)
	map<int, int> adj[501];	// 인접정점번호, 가중치
	vector<int> reverse_adj[501];	// adj[]의 역방향 그래프(가중치는 저장하지 않음)
	priority_queue<pair<int, int>> pq;	// 가중치(마이너스), 정점번호

	cin >> s >> d;

	for (int i = 0; i < m; i++) {
		int u, v, p;
		cin >> u >> v >> p;
		adj[u].insert({ v,p });
		reverse_adj[v].push_back(u);
	}

	fill_n(dist, n, INF);
	dist[s] = 0;
	pq.push({ 0, s });

	while (!pq.empty()) {
		int cur = pq.top().second;
		int cur_dist = -pq.top().first;
		pq.pop();
		
		// 이미 더 최단 거리를 구했다면 건너뜀
		if (dist[cur] < cur_dist) continue;

		for (auto& a : adj[cur]) {
			int next = a.first;
			int next_cost = a.second;

			if (dist[cur] + next_cost < dist[next]) {
				dist[next] = dist[cur] + next_cost;
				pq.push({ -dist[next], next });
			}
		}
	}

	// 최단경로 지우기
	queue<int> q_cut;
	q_cut.push(d);
	while (!q_cut.empty()) {
		int cut_to = q_cut.front();
		q_cut.pop();

		for (auto& cut_from : reverse_adj[cut_to]) {
			if (dist[cut_from] + adj[cut_from][cut_to] == dist[cut_to]) {
				adj[cut_from].erase(cut_to);
				if (cut_from != s) q_cut.push(cut_from);
			}
		}
	}

	fill_n(dist, n, INF);
	dist[s] = 0;
	pq.push({ 0, s });

	while (!pq.empty()) {
		int cur = pq.top().second;
		int cur_dist = -pq.top().first;
		pq.pop();

		if (dist[cur] < cur_dist) continue;

		for (auto& a : adj[cur]) {
			int next = a.first;
			int next_cost = a.second;

			if (dist[cur] + next_cost < dist[next]) {
				dist[next] = dist[cur] + next_cost;
				pq.push({ -dist[next], next });
			}
		}
	}

	return (dist[d] == INF) ? -1 : dist[d];
}

int main() {
	ios_base::sync_with_stdio(false);
	cin.tie(NULL);
	cout.tie(NULL);

	int n, m;
	while (true) {
		cin >> n >> m;
		if (n == 0 && m == 0) break;
		else cout << Dijkstra(n, m) << "\n";
	}
}
```
