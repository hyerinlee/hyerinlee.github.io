---
title: "Week13 - 스터디 문제집(2) (끝)"
date: 2021-12-14 16:30:00 +0900
categories: [Algorithm, GM5기_스터디_1]
tags: [baekjoon, cpp, dfs, euclidean_algorithm, kruskal_algorithm, mst, study, topological_sort, union_find]
---

## **📝 문제 목록**
- [🥈3] [1735 - 분수 합](https://www.acmicpc.net/problem/1735)
- [🥇4] [1197 - 최소 스패닝 트리](https://www.acmicpc.net/problem/1197)
- [🥇3] [2252 - 줄 세우기](https://www.acmicpc.net/problem/2252)

---

> 개인적으로 공부한 내용을 작성한 것으로 틀린 부분이 있을 수 있습니다  
틀린 부분이 있다면 지적해주시면 감사하겠습니다🙏  

<br>

스터디 끝난지가 한참됐는데 글을 이제야 쓴다. 무슨 생각하면서 풀었는지 다 까먹었는데 어쩌지-_-;; 그냥 그때 풀면서 끄적여뒀던 메모라도 좀 정리해서 적어봐야겠다.  

## **1. 분수 합**
두 분수의 합을 기약분수 형태로 나타내는 문제.  
그냥 분모를 최소공배수로 만들어 더한다음 분자와 분모의 최대공약수로 나누었다. 최대공약수는 재귀를 이용한 유클리드 호제법으로 구했다. 금방 풀었던 문제.  

### 코드
```cpp
#include<iostream>

using namespace std;

int GCD(int a, int b) {
	return ((a % b == 0)? b: GCD(b, a % b));
}

int main() {
	ios_base::sync_with_stdio(false);
	cin.tie(NULL);
	cout.tie(NULL);

	int numerator[2], denominator[2];
	cin >> numerator[0] >> denominator[0] >> numerator[1] >> denominator[1];

	int res_numer = numerator[0] * denominator[1] + numerator[1] * denominator[0];
	int res_denom = denominator[0] * denominator[1];
	int res_gcd = GCD(res_numer, res_denom);

	cout << res_numer / res_gcd << " " << res_denom / res_gcd << "\n";
}
```
<br>

## **2. 최소 스패닝 트리**
제목이 곧 내용이다.  
이건 아마 MST를 예전에 공부했었는데 기억이 잘 안나서 복습하고 다시 풀었던 것 같다. 크루스칼 알고리즘을 사용해 풀었고 과정을 간단히 설명하면 이렇다.  

1. 간선을 오름차순 정렬
2. 가중치가 적은것부터 체크하면서 양쪽 정점이 서로 다른 집합에 속해있을 때만 결과값에 넣는다(넣고 같은집합으로 만든다).
3. 2 작업을 v-1번 한다.

그런데 유니온파인드를 제대로 이해하지 못한 탓에 TLE와 WA를 한번씩 받았다.  
같은 그룹에 속해있는지 판단하기 위해 루트 노드를 비교할 때, 배열에 저장된 값으로 비교할 것이 아니라 Find()를 호출한 값으로 비교해야 한다.  

### 코드
```cpp
#include<iostream>
#include<vector>
#include<algorithm>

using namespace std;

int root[10001];

int Find(int index) {
	return ((root[index] == index) ? index : root[index] = Find(root[index]));
}

void Union(int index_a, int index_b) {
	index_a = Find(index_a);
	index_b = Find(index_b);
	root[index_b] = index_a;
}

int main() {
	ios_base::sync_with_stdio(false);
	cin.tie(NULL);
	cout.tie(NULL);

	int v, e;
	long long mst_weight = 0;
	vector < pair<int, pair<int, int>>> edges;	// 가중치, 노드A, 노드B
	cin >> v >> e;

	for (int i = 1; i <= v; i++) {
		root[i] = i;
	}

	for (int i = 0; i < e; i++) {
		int a, b, c;
		cin >> a >> b >> c;
		edges.push_back({ c,{a,b} });
	}

	sort(edges.begin(), edges.end());

	int cnt = 0;
	for (const auto& edge : edges) {
		if (cnt == v - 1) break;
		int a = edge.second.first;
		int b = edge.second.second;
		if (Find(a) != Find(b)) {
			mst_weight += edge.first;
			Union(a, b);
			cnt++;
		}
	}

	cout << mst_weight;
}
```
<br>

## **3. 줄 세우기**
두 학생의 인덱스와 둘의 키 대소관계만이 담긴 데이터가 M개 주어지면 그것을 이용해서 키 순서대로 정렬해야 하는 문제다.  
처음엔 트리를 사용해야 하나 했는데 그건 아니었고.. 뭔가 알고리즘이 그리 복잡하지는 않을 것 같은데 하는 생각은 들면서도 좋은 풀이가 영 떠오르지 않아 결국 알고리즘 힌트를 얻고 풀었다. 위상정렬 문제는 이번에 처음 접해서 먼저 개념부터 익혔는데, 생소한 용어에 겁을 먹어서 그렇지 풀이를 알고나니 그렇게 어렵지는 않았던 것 같다.  

일단 위상정렬에서 위상(Topological, `位相`) 이란 어떤 사물이 다른 사물과의 관계 속에서 갖는 위치나 양상을 말한다.  
즉 그냥 각 정점과의 관계를 고려하여 순서를 거스르지 않도록 정렬하란 뜻이다. 이때, 그래프 형태는 유향 비사이클 그래프(DAG)여야 한다.  
위상정렬의 대표적인 예가 대학의 선수과목 구조이다. 어떤 과목을 수강하기 위해서는 그전에 꼭 들어야 하는 과목이 있는 것처럼, 각 정점 간에 어떤 관계가 있으며 그것을 역행하는 경우가 없도록 순서대로 정렬이 되어야 한다.  
단, 답이 여러 가지일 수 있는데 문제에서는 그럴 경우 아무거나 출력하라고 되어 있다.  

여러 방법으로 위상정렬을 구현할 수 있지만 나는 DFS의 순회 결과를 뒤집는 방법을 사용했다.  
간단히 설명하자면 이렇다. 예를 들어 `2` 번이 `3` 번보다 크고, `1` 번이 `3` 번보다 크다고 하자. 그걸 그래프로 나타내면 아래와 같다.  

![001](/assets/img/post_images/20211214001.png)

답은 `1-2-3` 또는 `2-1-3` 일 것이다. `1` 과 `2` 의 대소관계는 모르지만 어쨌든 `3` 을 방문하기 전에는 항상 `1`, `2` 를 먼저 방문해야 한다.  
예시 그래프가 좀 단순하기는 한데, 먼저 방문해야 할 것들을 끝까지 방문해야 한다는 점에서 DFS를 응용한 것이다. 단, 그러기 위해 간선의 방향을 반대로 바꿔주어야 할 것이다. 그리고 이 문제에서는 키가 큰 순서대로 정렬하라고 했으므로 순회 결과를 반대로 출력해야 한다.  

### 코드
```cpp
#include<iostream>
#include<vector>

using namespace std;

bool is_visited[32001];
vector<int> edges[32001];

void DFS(const int& index, const int& n) {
	is_visited[index] = true;
	for (const auto& v : edges[index]) {
		if (!is_visited[v]) {
			DFS(v, n);
		}
	}
	cout << index << " ";
}

int main() {
	ios_base::sync_with_stdio(false);
	cin.tie(NULL);
	cout.tie(NULL);

	int n, m;
	cin >> n >> m;

	fill_n(is_visited, n, false);
	for(int i = 0; i < m; i++) {
		int a, b;
		cin >> a >> b;
		edges[b].push_back(a);
	}

	for (int i = 1; i <= n; i++) {
		if(!is_visited[i]) DFS(i, n);
	}
}
```
