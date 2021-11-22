---
title: "Week12 - 스터디 문제집(1)"
date: 2021-11-22 19:35:00 +0900
categories: [Algorithm, GM5기_스터디_1]
tags: [baekjoon, cpp, dfs, disjoint_set, study, two_pointer, union_find]
---

## **📝 문제 목록**
- [🥈3] [2003 - 수들의 합 2](https://www.acmicpc.net/problem/2003)
- [🥈1] [1991 - 트리 순회](https://www.acmicpc.net/problem/1991)
- [🥇4] [1717 - 집합의 표현](https://www.acmicpc.net/problem/1717)
- ~~[🥇1] [2042 - 구간 합 구하기](https://www.acmicpc.net/problem/2042)~~ (통과 X)

---

> 개인적으로 공부한 내용을 작성한 것으로 틀린 부분이 있을 수 있습니다  
틀린 부분이 있다면 지적해주시면 감사하겠습니다🙏  

<br>

## **1. 수들의 합 2**
사실 3주차 때 풀이에도 썼던 문젠데 그새 까먹었고=_=;; 투포인터를 사용해야겠다는 생각은 금방 했는데 알고리즘을 잘못 짜서 헤맸다.  
포인터를 양쪽 끝에 두고 이상하게 풀었다가 질문글에서 힌트를 얻고 다시 짜서 AC받았다... 지금 생각해보면 시간초과 나는게 너무나 당연한 코드였는데...제발 시간복잡도 생각하면서 풀자. 그리고 알고리즘 유형을 아는 게 도움은 되겠지만 너무 유형 맞추기식으로 접근하려고 하니까 실력이 안 느는게 아닐까 싶다.  
아무튼 아래 코드를 설명하자면 `left`, `right` 두 개의 포인터를 0번째에 놓고 구간합을 구하여 합이 기준값보다 작으면 `right` 를 오른쪽으로 한칸 밀고, 기준값보다 크면 `left` 를 오른쪽으로 한칸 밀면서 구해나가는 방식이다. 간단!  
데이터 정렬은 필요없다.  

### 코드
```cpp
#include<iostream>
#include<vector>

using namespace std;

int main() {
	ios_base::sync_with_stdio(false);
	cin.tie(NULL);
	cout.tie(NULL);

	int n, m, sum;
	int left_index = 0, right_index = 0;
	int count = 0;
	vector<int> a;
	cin >> n >> m;
	for (int i = 0; i < n; i++) {
		int num;
		cin >> num;
		a.push_back(num);
	}

	sum = a[0];
	while (right_index < n - 1 || sum >= m) {
		if (sum >= m) {
			if (sum == m) count++;
			sum -= a[left_index];
			left_index++;
		}
		else {
			sum += a[right_index + 1];
			right_index++;
		}
	}

	cout << count;
}
```
<br>

## **2. 트리 순회**
이진 트리를 입력받아서 전/중/후위 순회 결과를 출력하는 문제다.  
그런데 어차피 방문 순서가 같더라도 출력 순서를, 정확히는 루트 노드의 출력 순서를 어떻게 놓느냐에 따라 다르게 나올것이기 때문에  
사실상 탐색은 DFS로 한번만 했고, 재귀함수를 사용함으로써 현재 방문한 문자를 저장하는 시점만 각각 다르게 하여 각 배열에 넣어주었다.  
문자 배열에 현재 방문한 문자를 넣는 시점은 이렇다.  

- **전위 순회(preorder traversal)**: 왼쪽 자식 노드 방문(재귀호출) 전
- **중위 순회(inorder traversal)**: 왼쪽 자식 노드 방문 후, 오른쪽 자식 노드 방문 전
- **후위 순회(postorder traversal)**: 오른쪽 자식 노드 방문 후

### 코드
```cpp
#include<iostream>
#include<vector>

using namespace std;

int n;
vector<pair<int, int>> graph;
vector<char> traversal[3];

void DFS(const int& index) {
	char c = 'A' + index;

	traversal[0].push_back(c);	// preorder traversal
	if (graph[index].first != -1) DFS(graph[index].first);
	traversal[1].push_back(c);	// inorder traversal
	if (graph[index].second != -1) DFS(graph[index].second);
	traversal[2].push_back(c);	// postorder traversal
}

int main() {
	ios_base::sync_with_stdio(false);
	cin.tie(NULL);
	cout.tie(NULL);

	cin >> n;
	graph.assign(n, pair<int, int>(-1, -1));

	for (int i = 0; i < n; i++) {
		char p, l, r;
		cin >> p >> l >> r;
		if (l != '.') graph[p - 'A'].first = l - 'A';
		if (r != '.') graph[p - 'A'].second = r - 'A';
	}

	DFS(0);

	for (const auto& t : traversal) {
		for (const auto& c : t) {
			cout << c;
		}
		cout << endl;
	}
}
```
<br>

## **3. 집합의 표현**
당연히 문제에서 요구하는 연산을 단순하게 했다가는 시간초과가 난다. 하지만 시간을 줄일 방법을 생각해내지 못해서 결국 힌트를 얻고 풀었다.  
일단 이 문제를 해결하기 위해서는 집합을 최대한 단순화시킬 방법을 생각해야 한다.  
여기서 필요한 연산은 '집합 합치기' 와 '같은 집합인지 확인하기' 둘뿐이다. 즉 같은 원소를 공유하는지만 확인할 수 있게 합치면 된다는 뜻이다.  
그러니까 그 비교 대상 원소를 하나 잡아놓고 그것만 비교하면, 다시말해 각 집합의 대표 원소를 뽑아놓으면 아주 간단해질 것이다.  
비유하자면 조 이름이 없는 조별과제에서 어느 두 학생에게 조장이 누구냐고 물어서 같은 조원인지 확인하는 느낌...  
이게 분리 집합(Disjoint set)의 개념이다. 스터디에서는 다룬 적 없지만 예전에 한번 공부했던 적은 있는데 미처 떠올리지 못했다. 그냥 이번엔 복습한 데에 의의를 두기로 했다.  

그리고 알고리즘도 알고리즘이지만 출력 형식도 신경써야하는 것 같다. 아래 코드에서 fast IO를 사용하지 않으면 TLE가 난다.  

### 코드
```cpp
#include<iostream>

using namespace std;

int root[1'000'001];

int Find(const int& index) {
	if (root[index] == index) return index;
	else return root[index] = Find(root[index]);
}

void Union(int a, int b) {
	a = Find(a);
	b = Find(b);

	root[b] = a;
}

int main() {
	ios_base::sync_with_stdio(false);
	cin.tie(NULL);
	cout.tie(NULL);

	int n, m;
	cin >> n >> m;

	for (int i = 0; i <= n; i++) {
		root[i] = i;
	}

	for (int i = 0; i < m; i++) {
		int op, a, b;
		cin >> op >> a >> b;
		if (op == 0) Union(a, b);
		else {
			cout << ((Find(a) == Find(b)) ? "YES" : "NO") << "\n";
		}
	}
}
```
