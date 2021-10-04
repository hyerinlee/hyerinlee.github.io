---
title: "Week7 - Dijkstra"
date: 2021-10-05 01:20:00 +0900
categories: [Algorithm, GM5기_스터디_1]
tags: [baekjoon, cpp, dijkstra, shortest_path, study]
---

## **📝 문제 목록**
- [🥇5] [1753 - 최단경로](https://www.acmicpc.net/problem/1753)
- [🥇4] [10282 - 해킹](https://www.acmicpc.net/problem/10282)
- [🥇4] [4485 - 녹색 옷 입은 애가 젤다지?](https://www.acmicpc.net/problem/4485)

---

> 개인적으로 공부한 내용을 작성한 것으로 틀린 부분이 있을 수 있습니다  
틀린 부분이 있다면 지적해주시면 감사하겠습니다🙏  

<br>

## **시작하기 전에: 최단경로 알고리즘**
어중간한 타이밍에 얘기를 꺼낸 것 같지만... '최단경로'라는 이름의 문제가 나온김에 여러가지 최단경로 문제에 대해 정리해봐야겠다. 혹시나 헷갈리던 부분도 정리할 겸.  
일단 모든 최단경로 문제에서 구하고자 하는 모든 값은 결국 정점 A->B까지의 경로이다.  

- **단일 쌍(single-pair) 최단경로**: 한 정점 A에서 다른 한 정점 B까지의 최단경로

이를 기본 목표로 하여 출발/도착정점의 가짓수에 따라 아래와 같은 유형들로 분류된다.  

- **단일 출발(single-source) 최단경로**: 한 정점 A에서 다른 모든 정점까지의 최단경로.
- **단일 도착(single-destination) 최단경로**: 모든 정점에서 한 정점 A까지의 최단경로. 간선의 방향을 반대로 하면 단일 출발 최단경로 문제와 같다.
-  **전체 쌍(all-pair) 최단경로**: 모든 정점에서 다른 모든 정점까지의 최단 경로.

그리고 이런 문제들을 해결하기 위한 주요 알고리즘으로는 다음과 같은 것들이 있다.  
(이때 간선에 방향이나 가중치가 있는지, 가중치에 음수가 포함되는지, 간선의 개수가 많은지 적은지 등 그래프의 간선의 특성에 따라서도 사용 알고리즘과 자료구조가 다르다.)  

- **BFS**: 가중치가 같거나 없는 그래프에서 최단경로 구하기
- **다익스트라**: (음의 값이 없는)가중치를 갖는 단일 출발 최단경로 구하기
- **벨만 포드**: 방향,가중치를 갖는 단일 출발 최단경로 구하기(음의 사이클을 주의)
- **A\***: 단일 쌍 최단경로 구하기(탐색 속도를 높이기 위해 휴리스틱 사용)
- **플로이드 워셜**: 전체 쌍 최단경로 구하기(단 음의 사이클이 없어야 함)

<br>

## **1. 최단경로**
이번 주차는 어쩌다 포스팅을 계속해서 미룬 탓에 문제풀때의 기억이 거의 사라져서 아무래도 풀던 당시의 생각이나 헤맸던 점을 다 적기는 어려울 것 같다ㅠㅠ  

이 문제는 자연수 가중치가 있는 간선을 갖는 그래프에서 단일 출발 최단경로를 구하는, 아주 대표적인 다익스트라 문제이다.  
먼저 생각해야 할 것은 가중치가 있는 그래프에서 연결되지 않았거나 아직 확인되지 않은 두 정점 사이의 거리를 어떤 값으로 표현하냐는 것인데, `INF(무한대)`로 지정해놓으면 알고리즘에 의해 더 짧은 거리를 발견했을 때 갱신되도록 할 수 있다.  
아무튼 그러고나서 모든 정점에 대하여 '시작점 K에서부터 정점까지의 거리(이하 dist)'를 갱신해나간다. 매회 미방문 정점 중 K와 가장 가까운곳 즉 dist가 최소인 정점을 찾아 그와 인접한 정점들의 dist를 갱신하는 방식으로 진행한다.  

이 알고리즘의 시간복잡도는 다음과 같다.  
```
① 모든 간선들을 한번씩 검사 => O(E)
② 미방문 정점 중 dist가 최소인 정점 찾기 => O(ElogE)
따라서 O(E + ElogE) = O(ElogE) (*대개의 경우 E<V^2이므로 O(ElogV)라고 보는듯하다.)
```
여기서 ②는 인접리스트와 우선순위 큐를 사용했을 때의 시간복잡도이다.  
우선순위 큐를 사용하지 않고 매번 모든 정점의 dist를 비교하는 식으로 탐색할 수도 있지만  (O(V^2)), 정점의 수가 적거나 간선이 매우 많은 경우가 아닌 이상 우선순위 큐를 사용하는 것이 빠르다.  

우선순위 큐를 사용해 정점 i에 대해 (dist[i], i)를 저장하여 dist를 기준으로 오름차순 정렬되도록 구현하는데, 이때 우선순위 큐는 기본적으로 내림차순이므로 큐에 넣을 때 dist[i]에 마이너스를 곱해주거나 변수 선언 시 오름차순 정렬되도록 써주어야 한다. (아래 코드 참고)  
큐에 데이터를 넣는 기준은 '거리가 갱신되었을 때' 이다.  

그런데 좀 헷갈린 부분이 있었다. 바로 "방문체크를 해야 하는가?"에 대한 문제였는데, 코드에서도 보이듯이 사실 나는 굳이 방문체크를 하지 않아도 된다고 생각해서 따로 뭔가를 해주지 않았었다.  
왜냐하면 큐에 같은 정점이 여러번 들어가더라도 최초 pop되었을 때 이미 인접정점에 대한 최종 dist를 구해버렸기 때문에 이후 다시 방문하더라도 인접정점의 dist를 갱신하고 큐에 넣는 일은 어차피 없을 것이므로 결과에 영향을 주지 않는다고 판단했기 때문이다.  
물론 아래 코드로도 통과하긴 했다. 그러나 결과는 같더라도 어쨌든 pop될 때마다 그 정점에 대한 모든 인접정점을 검사하기는 할 것이니 무의미한 작업으로 실행시간에 영향을 주는 것을 막기 위해 인접정점 검사 전 건너뛰기 위한 조건문은 추가해주는게 좋을 것이다. 뭐 bool 배열을 추가해서 체크를 하든지, 아래 코드 주석처럼 dist 값을 비교하든지...  
([참고 블로그 글](https://m.blog.naver.com/kks227/220796029558))  

### 코드
```cpp
#include<iostream>
#include<vector>
#include<queue>

using namespace std;

const int INF = 99999999;
int dist[20001];
vector<pair<int, int>> adj[20001];	// (인접)정점번호, 가중치
priority_queue< pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>> > pq;	// dist[정점], 정점번호


int main() {
	ios_base::sync_with_stdio(false);
	cin.tie(NULL);
	cout.tie(NULL);

	int v, e, k;
	cin >> v >> e >> k;

	fill_n(&dist[1], v, INF);

	for (int i = 0; i < e; i++) {
		int f, t, d;
		cin >> f >> t >> d;
		adj[f].push_back({ t,d });
	}

	// 시작노드
	dist[k] = 0;
	pq.push({ 0, k });


	while (!pq.empty()) {
		int cur = pq.top().second;
		int cur_dist = pq.top().first;
		pq.pop();
		
		// * 이때 dist[cur]<cur_dist라면 continue 해주기

		for (int i = 0; i < adj[cur].size(); i++) {
			int next = adj[cur][i].first;
			int next_adj = adj[cur][i].second;
			if (cur_dist + next_adj < dist[next]) {
				dist[next] = cur_dist + next_adj;
				pq.push({ dist[next], next });
			}
		}
	}

	for (int i = 1; i <= v; i++) {
		if (dist[i] == INF) cout << "INF";
		else cout << dist[i];
		cout << "\n";
	}
}
```
<br>

## **2. 해킹**
컴퓨터의 수와 컴퓨터들끼리의 의존성 정보, 그리고 맨 처음 해킹당한 컴퓨터의 번호가 주어지면
총 감염된 컴퓨터 수와 마지막 컴퓨터가 감염되기까지 걸리는 시간을 출력하는 문제이다.  
위 문제와 비슷한데 웃긴건 여기서는 방문체크를 해줬으면서 또 조건문은 안 넣어줬다는거다=_=;; 그럼 왜쓴걸까...카운트때문에 넣은건지...근데 어차피 마지막에 INF 아니면 올리면 되는데 굳이?ㅋㅋ 암튼...마지막 컴퓨터가 감염되기까지 걸리는 시간은 감염된 컴퓨터 중 가장 먼 것의 감염시간일 것이다.  
그리고 매 테스트케이스마다 배열 값을 초기화해주는 것도 잊지 않았다.  

### 코드
```cpp
#include<iostream>
#include<vector>
#include<queue>

using namespace std;

// 방향, 가중치 있음

const int INF = 99999999;
bool is_infected[10001];
int time[10001];
vector<pair<int, int>> adj[10001];	// 가중치(a->b 감염시간), (인접)정점번호
priority_queue< pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>> > pq;	// time[정점], 정점번호

void dijkstra() {
	int cnt = 0;
	int end_time = 0;

	int n, d, c;
	cin >> n >> d >> c;

	fill_n(&is_infected[1], n, false);
	fill_n(&time[1], n, INF);
	for (int i = 1; i <= n; i++) {
		adj[i].clear();
	}

	for (int i = 0; i < d; i++) {
		int a, b, s;
		cin >> a >> b >> s;
		adj[b].push_back({ s,a });
	}

	// 시작정점
	time[c] = 0;
	pq.push({ time[c],c });

	while (!pq.empty()) {
		int cur = pq.top().second;
		int cur_t = pq.top().first;
		pq.pop();
		
		// * 감염되었으면 continue

		is_infected[cur] = true;

		for (int i = 0; i < adj[cur].size(); i++) {
			int next = adj[cur][i].second;
			int next_t = adj[cur][i].first;

			if (cur_t + next_t < time[next]) {
				time[next] = cur_t + next_t;
				pq.push({ time[next],next });
			}
		}
	}

	for (int i = 1; i <= n; i++) {
		if (is_infected[i]) cnt++;
		if (time[i] < INF && time[i] > end_time) {
			end_time = time[i];
		}
	}

	cout << cnt << " " << end_time << "\n";
}

int main() {
	ios_base::sync_with_stdio(false);
	cin.tie(NULL);
	cout.tie(NULL);

	int num;
	cin >> num;

	for (int i = 0; i < num; i++) {
		dijkstra();
	}
}
```
<br>

## **3. 녹색 옷 입은 애가 젤다지?😂**
각 칸을 방문했을 때 잃는 금액이 적힌 N×N 크기 맵이 주어지면, 왼쪽 위 끝에서 오른쪽 아래 끝으로 갈 때까지 최소한 얼마를 잃게 되는지 구하는 문제다.  

이 문제는 위 두 문제들과 달리 각 칸에 값이 주어져있고 상하좌우를 살펴보는 형식의 문제이다. 그래서 우선 상하좌우를 차례로 들리기 위해 x,y 이동좌표를 담은 dir 배열을 하나 만들어주고, 이후 조건문에서 상하좌우를 들렀을 때 그 칸이 맵 내에 있는 칸인지도 검사해줬다.  

그리고 다음 칸에 저장되어 있는 값을 가중치라고 생각해서, 시작점에서 어떤 칸까지 가는 데 잃는 최소 금액을 result라고 했을 때  
다음 칸의 result보다 지금 밟고있는 칸을 통해 다음 칸으로 갔을때의 금액이 더 적다면 그 값을 갱신함과 동시에 다음 칸의 좌표를 큐에 넣도록 했다.  
이때 값을 비교하여 한번 방문했다면 더 이상 방문하지 않도록 조건문을 넣어주었다.  

그러고나서 (n,n)좌표의 result를 출력해주면 된다. 표현이나 출력해야 할 데이터가 다를 뿐 결국 최소비용을 구하는 건 똑같다.  

### 코드
```cpp
#include<iostream>
#include<queue>

using namespace std;

const int INF = 99999999;

int main() {
	int num = 1;
	int lost[126][126];
	int dir[4][2] = { {-1,0},{0,-1},{0,1},{1,0} };
	priority_queue<pair<int, pair<int, int>>> pq;	// x, y

	while (true) {
		int n;
		int result[126][126];
		cin >> n;
		if (n == 0) break;

		for (int i = 1; i <= n; i++) {
			for (int j = 1; j <= n; j++) {
				cin >> lost[i][j];
			}
		}

		for (int i = 1; i <= n; i++) {
			fill_n(&result[i][1], n, INF);
		}

		result[1][1] = lost[1][1];
		pq.push({ -lost[1][1], { 1,1 } });

		while (!pq.empty()) {
			int cur_res = -pq.top().first;
			int cur_x = pq.top().second.first;
			int cur_y = pq.top().second.second;
			pq.pop();
			if (result[cur_x][cur_y] < cur_res) continue;

			for (int i = 0; i < 4; i++) {
				int next_x = cur_x + dir[i][0];
				int next_y = cur_y + dir[i][1];

				if (next_x > 0 && next_x <= n && next_y > 0 && next_y <= n &&
					cur_res + lost[next_x][next_y] < result[next_x][next_y]) {
					result[next_x][next_y] = result[cur_x][cur_y] + lost[next_x][next_y];
					pq.push({ -result[next_x][next_y], { next_x,next_y } });
				}
			}
		}

		cout << "Problem " << num++ << ": " << result[n][n] << "\n";
	}
}
```
