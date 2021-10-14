---
title: "Week8 - Bellman Ford"
date: 2021-10-15 01:30:00 +0900
categories: [Algorithm, GM5기_스터디_1]
tags: [baekjoon, bellman_ford, cpp, study]
---

## **📝 문제 목록**
- [🥇4] [11657 - 타임머신](https://www.acmicpc.net/problem/11657)
- [🥇3] [1865 - 웜홀](https://www.acmicpc.net/problem/1865)

---

> 개인적으로 공부한 내용을 작성한 것으로 틀린 부분이 있을 수 있습니다  
틀린 부분이 있다면 지적해주시면 감사하겠습니다🙏  

<br>

## **1. 타임머신**
단일 출발 최단경로 문제이고, 제목에서도 알 수 있듯 간선의 가중치가 음수일 수도 있는 문제이다. 또한 문제에서 간선은 방향을 갖고 있다.  
따라서 벨만포드 알고리즘을 사용했다. 다익스트라 알고리즘은 음수 사이클이 있으면 최단경로를 구할 수 없고, 음수 사이클이 없는 그래프에서 구할 방법이 있다고는 해도 벨만포드보다 성능이 좋지 않으므로 굳이 쓰지 않는다.  
동일한 값을 더해주어 가중치를 모두 양수로 만들어주는 방법도 생각해볼 수 있지만 그렇게 하면 지나온 간선의 수만큼 값이 더해지기 때문에 자칫 틀린 값이 나올 수 있어 따로 또 처리를 해주거나 해야 한다. (예를 들면 더 많은 간선을 거치는 더 짧은 경로가 있을 경우)  
(참고: [https://www.acmicpc.net/board/view/19865](https://www.acmicpc.net/board/view/19865))  

벨만포드 알고리즘에서 음수 사이클을 찾아내는 방법은 다음과 같다.  
1. V-1번의 간선 완화 작업 => 모든 정점에 대한 최단경로 구하기가 끝난다.
2. 마지막으로 한번 더 간선 완화 작업 => 여기서 값이 갱신되면 음수 사이클이 있다는 뜻이다.

간선 완화를 V-1번 해주는 이유는 이렇다.  
일단 우리는 최단경로를 구하기 위한 최적의 방문 순서를 알 수 없다. 한번에 모든 정점에 대한 최단경로가 구해질수도 있지만, 방문 순서에 따라 여러번 반복해야만 최단경로가 구해지는 경우도 있다.  
[https://sodocumentation.net/algorithm/topic/4791/bellman-ford-algorithm#why-do-we-need-to-relax-all-the-edges-at-most--v-1--times](https://sodocumentation.net/algorithm/topic/4791/bellman-ford-algorithm#why-do-we-need-to-relax-all-the-edges-at-most--v-1--times)  
(위 링크는 최악의 순서를 보여주고 있다.)  
하지만 이렇게 최악의 순서로 진행하더라도 한번 완화할 때마다 정점 하나씩은 최종 최단경로가 구해진다. 즉 최악의 방문 순서에서도 V-1번을 반복하면 모든 정점의 최단경로가 구해지게 되는 것이다.  

그래서 시간복잡도는 모든 간선을 순회(O(E))하는 작업을 V-1번 반복하므로(O(V)) => O(VE)이다.  

간선 완화 시 주의했던 점은 이런 것들이 있었다.  
1. **한번이라도 거리가 계산된 적 있는(=시작점에서 도달 가능하다고 판단된) 정점에 한해서만 완화 작업을 수행함:**  
그렇지 않으면, 시작점에서 도달할 수 없는 어느 정점 A,B가 있다고 했을 때 둘 사이에 음수 사이클이 있다면 무한대로 저장되어 있던 거리값이 계속해서 줄어들면서 이상한 결과가 나올 수 있기 때문이다.
2. **최종 결과값을 저장하는 자료형이 long long이어야 함:**  
이 문제에서 주어진 가중치에 따르면, 갱신을 반복하다가 int의 범위를 벗어나는 경우가 생길 가능성이 있다. 따라서 언더플로우가 발생하면 잘못된 결과가 나올 수 있다.

### 코드
```cpp
#include<iostream>
#include<vector>

using namespace std;

int main() {
	ios_base::sync_with_stdio(false);
	cin.tie(NULL);
	cout.tie(NULL);

	const int INF = 99999999;		// 무한
	bool has_cycle = false;			// 음수 사이클 체크
	int n, m;
	long long dist[501];			// 최종 결과값
	vector<pair<int, int>> d[501];	// 간선 정보

	cin >> n >> m;

	for (int i = 0; i < m; i++) {
		int a, b, c;
		cin >> a >> b >> c;
		d[a].push_back({ b, c });
	}

	fill_n(dist, n+1, INF);
	dist[1] = 0;

	for (int i = 0; i < n - 1; i++) {
		for (int here = 1; here <= n; here++) {
			if (dist[here] == INF) continue;
			for (auto& p : d[here]) {
				int there = p.first;
				int t = p.second;
				if (dist[here] + t < dist[there]) dist[there] = dist[here] + t;
			}
		}
	}

	// 음수 사이클이 있는지 확인
	for (int here = 1; here <= n; here++) {
		if (dist[here] == INF) continue;
		for (auto& p : d[here]) {
			int there = p.first;
			int t = p.second;
			if (dist[here] + t < dist[there]) has_cycle = true;
		}
	}

	if (has_cycle) cout << -1;
	else {
		for (int i = 2; i <= n; i++) {
			cout << ((dist[i] == INF) ? -1 : dist[i]) << "\n";
		}
	}
}
```

<br>

## **2. 웜홀**
이 문제의 특징은 이러했다.  
1. 목표는 최단거리 구하기(X) -> 음수 사이클이 있는지를 체크(O) 하는 것이다.
2. 시작점이 따로 정해져있지 않다.
3. "도로는 방향이 없으며 웜홀은 방향이 있다."

일단 이 문제에서는 음수 사이클이 있는지에만 관심이 있으므로 어떤 점에서 다른 점에 도달할 수 있는지는 신경쓰지 않아도 된다. 애초에 시작점도 따로 정해져있지 않고 그냥 다시 자기 위치로 돌아왔을 때 시간이 줄어드는 경우가 있는지만 알아보면 된다(모든 정점에 대해서).  
따라서 여기서는 최단거리 값을 무한대가 아니라 0으로 초기화해 주었고, 방문한 정점에 한해서만 완화 작업을 수행하게 하는 조건문도 없앴다.  
그리고 문제에 적혀있던대로 도로는 무방향임을 고려하여 입력받을 때 반대 방향 간선 정보도 같이 저장해주었다.  

코드를 보면 앞 문제에서는 알고리즘이 익숙하지 않아 헷갈리지 않기 위해 n-1번 완화를 수행하고 음수 사이클을 확인하는 과정을 따로 빼주었지만 여기서는 코드를 줄여 n번 수행하고 마지막에 음수 사이클을 확인하도록 했다.  

### 코드
```cpp
#include<iostream>
#include<vector>

using namespace std;

int main() {
	ios_base::sync_with_stdio(false);
	cin.tie(NULL);
	cout.tie(NULL);

	int tc;
	cin >> tc;

	for (int i = 0; i < tc; i++) {
		bool has_cycle = false;
		int n, m, w;
		cin >> n >> m >> w;

		long long dist[501];
		vector<pair<int, int>> d[501];

		for (int i = 1; i <= m + w; i++) {
			int s, e, t;
			cin >> s >> e >> t;
			if (i <= m) {
				d[s].push_back({ e,t });
				d[e].push_back({ s,t });
			}
			else d[s].push_back({ e,-t });
		}

		fill_n(dist, n + 1, 0);

		for (int i = 1; i <= n; i++) {
			for (int here = 1; here <= n; here++) {
				for (auto& p : d[here]) {
					int there = p.first;
					int d = p.second;

					if (dist[here] + d < dist[there]) {
						if (i == n) has_cycle = true;
						else dist[there] = dist[here] + d;
					}
				}
			}
		}

		cout << ((has_cycle) ? "YES" : "NO") << "\n";
	}
}
```
