---
title: "Week8 - Bellman Ford"
date: 2021-10-15 01:30:00 +0900
categories: [Algorithm, GM5κΈ°_μ€ν°λ_1]
tags: [baekjoon, bellman_ford, cpp, study]
---

## **π λ¬Έμ  λͺ©λ‘**
- [π₯4] [11657 - νμλ¨Έμ ](https://www.acmicpc.net/problem/11657)
- [π₯3] [1865 - μν](https://www.acmicpc.net/problem/1865)

---

> κ°μΈμ μΌλ‘ κ³΅λΆν λ΄μ©μ μμ±ν κ²μΌλ‘ νλ¦° λΆλΆμ΄ μμ μ μμ΅λλ€  
νλ¦° λΆλΆμ΄ μλ€λ©΄ μ§μ ν΄μ£Όμλ©΄ κ°μ¬νκ² μ΅λλ€π  

<br>

## **1. νμλ¨Έμ **
λ¨μΌ μΆλ° μ΅λ¨κ²½λ‘ λ¬Έμ μ΄κ³ , μ λͺ©μμλ μ μ μλ― κ°μ μ κ°μ€μΉκ° μμμΌ μλ μλ λ¬Έμ μ΄λ€. λν λ¬Έμ μμ κ°μ μ λ°©ν₯μ κ°κ³  μλ€.  
λ°λΌμ λ²¨λ§ν¬λ μκ³ λ¦¬μ¦μ μ¬μ©νλ€. λ€μ΅μ€νΈλΌ μκ³ λ¦¬μ¦μ μμ μ¬μ΄ν΄μ΄ μμΌλ©΄ μ΅λ¨κ²½λ‘λ₯Ό κ΅¬ν  μ μκ³ , μμ μ¬μ΄ν΄μ΄ μλ κ·Έλνμμ κ΅¬ν  λ°©λ²μ΄ μλ€κ³ λ ν΄λ λ²¨λ§ν¬λλ³΄λ€ μ±λ₯μ΄ μ’μ§ μμΌλ―λ‘ κ΅³μ΄ μ°μ§ μλλ€.  
λμΌν κ°μ λν΄μ£Όμ΄ κ°μ€μΉλ₯Ό λͺ¨λ μμλ‘ λ§λ€μ΄μ£Όλ λ°©λ²λ μκ°ν΄λ³Ό μ μμ§λ§ κ·Έλ κ² νλ©΄ μ§λμ¨ κ°μ μ μλ§νΌ κ°μ΄ λν΄μ§κΈ° λλ¬Έμ μμΉ« νλ¦° κ°μ΄ λμ¬ μ μμ΄ λ°λ‘ λ μ²λ¦¬λ₯Ό ν΄μ£Όκ±°λ ν΄μΌ νλ€. (μλ₯Ό λ€λ©΄ λ λ§μ κ°μ μ κ±°μΉλ λ μ§§μ κ²½λ‘κ° μμ κ²½μ°)  
(μ°Έκ³ : [https://www.acmicpc.net/board/view/19865](https://www.acmicpc.net/board/view/19865))  

λ²¨λ§ν¬λ μκ³ λ¦¬μ¦μμ μμ μ¬μ΄ν΄μ μ°Ύμλ΄λ λ°©λ²μ λ€μκ³Ό κ°λ€.  
1. V-1λ²μ κ°μ  μν μμ => λͺ¨λ  μ μ μ λν μ΅λ¨κ²½λ‘ κ΅¬νκΈ°κ° λλλ€.
2. λ§μ§λ§μΌλ‘ νλ² λ κ°μ  μν μμ => μ¬κΈ°μ κ°μ΄ κ°±μ λλ©΄ μμ μ¬μ΄ν΄μ΄ μλ€λ λ»μ΄λ€.

κ°μ  μνλ₯Ό V-1λ² ν΄μ£Όλ μ΄μ λ μ΄λ λ€.  
μΌλ¨ μ°λ¦¬λ μ΅λ¨κ²½λ‘λ₯Ό κ΅¬νκΈ° μν μ΅μ μ λ°©λ¬Έ μμλ₯Ό μ μ μλ€. νλ²μ λͺ¨λ  μ μ μ λν μ΅λ¨κ²½λ‘κ° κ΅¬ν΄μ§μλ μμ§λ§, λ°©λ¬Έ μμμ λ°λΌ μ¬λ¬λ² λ°λ³΅ν΄μΌλ§ μ΅λ¨κ²½λ‘κ° κ΅¬ν΄μ§λ κ²½μ°λ μλ€.  
[https://sodocumentation.net/algorithm/topic/4791/bellman-ford-algorithm#why-do-we-need-to-relax-all-the-edges-at-most--v-1--times](https://sodocumentation.net/algorithm/topic/4791/bellman-ford-algorithm#why-do-we-need-to-relax-all-the-edges-at-most--v-1--times)  
(μ λ§ν¬λ μ΅μμ μμλ₯Ό λ³΄μ¬μ£Όκ³  μλ€.)  
νμ§λ§ μ΄λ κ² μ΅μμ μμλ‘ μ§ννλλΌλ νλ² μνν  λλ§λ€ μ μ  νλμ©μ μ΅μ’ μ΅λ¨κ²½λ‘κ° κ΅¬ν΄μ§λ€. μ¦ μ΅μμ λ°©λ¬Έ μμμμλ V-1λ²μ λ°λ³΅νλ©΄ λͺ¨λ  μ μ μ μ΅λ¨κ²½λ‘κ° κ΅¬ν΄μ§κ² λλ κ²μ΄λ€.  

κ·Έλμ μκ°λ³΅μ‘λλ λͺ¨λ  κ°μ μ μν(O(E))νλ μμμ V-1λ² λ°λ³΅νλ―λ‘(O(V)) => O(VE)μ΄λ€.  

κ°μ  μν μ μ£Όμνλ μ μ μ΄λ° κ²λ€μ΄ μμλ€.  
1. **νλ²μ΄λΌλ κ±°λ¦¬κ° κ³μ°λ μ  μλ(=μμμ μμ λλ¬ κ°λ₯νλ€κ³  νλ¨λ) μ μ μ νν΄μλ§ μν μμμ μνν¨:**  
κ·Έλ μ§ μμΌλ©΄, μμμ μμ λλ¬ν  μ μλ μ΄λ μ μ  A,Bκ° μλ€κ³  νμ λ λ μ¬μ΄μ μμ μ¬μ΄ν΄μ΄ μλ€λ©΄ λ¬΄νλλ‘ μ μ₯λμ΄ μλ κ±°λ¦¬κ°μ΄ κ³μν΄μ μ€μ΄λ€λ©΄μ μ΄μν κ²°κ³Όκ° λμ¬ μ μκΈ° λλ¬Έμ΄λ€.
2. **μ΅μ’ κ²°κ³Όκ°μ μ μ₯νλ μλ£νμ΄ long longμ΄μ΄μΌ ν¨:**  
μ΄ λ¬Έμ μμ μ£Όμ΄μ§ κ°μ€μΉμ λ°λ₯΄λ©΄, κ°±μ μ λ°λ³΅νλ€κ° intμ λ²μλ₯Ό λ²μ΄λλ κ²½μ°κ° μκΈΈ κ°λ₯μ±μ΄ μλ€. λ°λΌμ μΈλνλ‘μ°κ° λ°μνλ©΄ μλͺ»λ κ²°κ³Όκ° λμ¬ μ μλ€.

### μ½λ
```cpp
#include<iostream>
#include<vector>

using namespace std;

int main() {
	ios_base::sync_with_stdio(false);
	cin.tie(NULL);
	cout.tie(NULL);

	const int INF = 99999999;		// λ¬΄ν
	bool has_cycle = false;			// μμ μ¬μ΄ν΄ μ²΄ν¬
	int n, m;
	long long dist[501];			// μ΅μ’ κ²°κ³Όκ°
	vector<pair<int, int>> d[501];	// κ°μ  μ λ³΄

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

	// μμ μ¬μ΄ν΄μ΄ μλμ§ νμΈ
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

## **2. μν**
μ΄ λ¬Έμ μ νΉμ§μ μ΄λ¬νλ€.  
1. λͺ©νλ μ΅λ¨κ±°λ¦¬ κ΅¬νκΈ°(X) -> μμ μ¬μ΄ν΄μ΄ μλμ§λ₯Ό μ²΄ν¬(O) νλ κ²μ΄λ€.
2. μμμ μ΄ λ°λ‘ μ ν΄μ Έμμ§ μλ€.
3. "λλ‘λ λ°©ν₯μ΄ μμΌλ©° μνμ λ°©ν₯μ΄ μλ€."

μΌλ¨ μ΄ λ¬Έμ μμλ μμ μ¬μ΄ν΄μ΄ μλμ§μλ§ κ΄μ¬μ΄ μμΌλ―λ‘ μ΄λ€ μ μμ λ€λ₯Έ μ μ λλ¬ν  μ μλμ§λ μ κ²½μ°μ§ μμλ λλ€. μ μ΄μ μμμ λ λ°λ‘ μ ν΄μ Έμμ§ μκ³  κ·Έλ₯ λ€μ μκΈ° μμΉλ‘ λμμμ λ μκ°μ΄ μ€μ΄λλ κ²½μ°κ° μλμ§λ§ μμλ³΄λ©΄ λλ€(λͺ¨λ  μ μ μ λν΄μ).  
λ°λΌμ μ¬κΈ°μλ μ΅λ¨κ±°λ¦¬ κ°μ λ¬΄νλκ° μλλΌ 0μΌλ‘ μ΄κΈ°νν΄ μ£Όμκ³ , λ°©λ¬Έν μ μ μ νν΄μλ§ μν μμμ μννκ² νλ μ‘°κ±΄λ¬Έλ μμ΄λ€.  
κ·Έλ¦¬κ³  λ¬Έμ μ μ νμλλλ‘ λλ‘λ λ¬΄λ°©ν₯μμ κ³ λ €νμ¬ μλ ₯λ°μ λ λ°λ λ°©ν₯ κ°μ  μ λ³΄λ κ°μ΄ μ μ₯ν΄μ£Όμλ€.  

μ½λλ₯Ό λ³΄λ©΄ μ λ¬Έμ μμλ μκ³ λ¦¬μ¦μ΄ μ΅μνμ§ μμ ν·κ°λ¦¬μ§ μκΈ° μν΄ n-1λ² μνλ₯Ό μννκ³  μμ μ¬μ΄ν΄μ νμΈνλ κ³Όμ μ λ°λ‘ λΉΌμ£Όμμ§λ§ μ¬κΈ°μλ μ½λλ₯Ό μ€μ¬ nλ² μννκ³  λ§μ§λ§μ μμ μ¬μ΄ν΄μ νμΈνλλ‘ νλ€.  

### μ½λ
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
