---
title: "Week3 - Binary Search, Two Pointer"
date: 2021-08-22 15:10:00 +0900
categories: [Algorithm, GM5๊ธฐ_์คํฐ๋_1]
tags: [baekjoon, binary_search, cpp, study, two_pointer]
---

## **๐ ๋ฌธ์  ๋ชฉ๋ก**
- [------] [15641 - SUPER SUPER BINARY SEARCH DELUXE 2.5: THE LEGEND OF THE GOLDEN MAZASSUMNIDA, EPISODE 2: THE MAZWAETL UNIVERSE, PART 2: THE PARALLEL UNIVERSE AND THE LOST MAZASSUMNIDA: GAME OF THE YEAR EDITION](https://www.acmicpc.net/problem/15641)
- [๐ฅ3] [1654 - ๋์  ์๋ฅด๊ธฐ](https://www.acmicpc.net/problem/1654)
- [๐ฅ5] [2467 - ์ฉ์ก](https://www.acmicpc.net/problem/2467)
- [๐ฅ4] [2473 - ์ธ ์ฉ์ก](https://www.acmicpc.net/problem/2473)

---
<br>

## **1. SUPER SUPER BINARY SEARCH DELUXE 2.5: THE LEGEND OF THE GOLDEN MAZASSUMNIDA, EPISODE 2: THE MAZWAETL UNIVERSE, PART 2: THE PARALLEL UNIVERSE AND THE LOST MAZASSUMNIDA: GAME OF THE YEAR EDITION**
๊ตฌ๋ฐ๊ธฐ์ปต์ ๋ค์ด๋ง ๋ดค์ง ๋ฌธ์ ๋ฅผ ํ์ด(?)๋ณธ ๊ฑด ์ด๋ฒ์ด ์ฒ์์ด๋ค. (๋ฌธ์  ์ด๋ฆ๋ถํฐ๊ฐใใ  
๋ด๊ฐ ์ง์  ์ด๋ถํ์๊ธฐ๊ฐ ๋์ด ์ ๋ต๊ฐ์ ์ฐพ๋ ๋ฌธ์ . 1~100 ์ฌ์ด์ ์์ฐ์๋ฅผ ์จ์ ์ ์ถํ๋ฉด ์ ๋ต๋ณด๋ค ํฐ์ง ์์์ง ์ฑ์  ํ๋ก๊ทธ๋จ์ด ํผ์ผํฐ์ง๋ก ์๋ ค์ค๋ค.  

### ์ฝ๋
```cpp
๐๐ญ
```
<br>

## **2. ๋์  ์๋ฅด๊ธฐ**
ํ์ด๋ณธ ์ ํ์ด๋ผ ํ์ด๋ ์๊ณ  ์์์ผ๋ฏ๋ก ๋ฐ๋ก ๋์ด๊ฐ ์ค ์์๋๋ฐ ์ด์คํ๊ฒ ์๊ณ  ์๋ ํ์ ํฌ์ธํฐ ์ด๋ ์ง์ ์ ์๋ชป ์ก๊ณ ... ์ค์ํ ๋ถ๋ถ๋ ๋นผ๋จน์ด๋ฒ๋ ค์ ํ์ฐธ ์จ๋ฆํ๋ ๋ฌธ์ ๋ค.  
์ผ๋จ '์ด๋ถํ์ ๋์=๋ง๋ค ์ ์๋ ๋์ ์ ์ต๋ ๊ธธ์ด'๋ก ๋๊ณ  ๊ทธ ๊ธธ์ด๋ก ์๋๋ค๊ณ  ๊ฐ์ ํ์ ๋ ๋ง๋ค์ด์ง๋ ๊ฐ์๋ฅผ ์นด์ดํธํ๋ฉด์ left, right๋ก ๊ธธ์ด์ ๋ฒ์๋ฅผ ์ขํ๋๊ฐ๋ ๊ฒ๊น์ง๋ ์๊ฐํ๋ค.  
๊ทธ๋ฐ๋ฐ left์ right๋ฅผ ์ฎ๊ธฐ๋ค๊ฐ ์ ๋ต์ ํฌํจ์ํค์ง ์๋ ๊ตฌ๊ฐ์ด ๋์ด๋ฒ๋ฆฌ๋ฉด mid๊ฐ์ด ๋ ์ด์ ์ ๋ต์ด ๋์ฌ ์ ์๊ฒ ๋๋ฏ๋ก, ๊ทธ๋ฌ๊ธฐ ์ ๊น์ง N๊ฐ ์ด์์ ๋ง๋ค ์ ์๋ ๊ธธ์ด์ ํํด ๊ทธ ๊ฐ์ ๋ฐ๋ก ์ ์ฅํ์ด์ผ ํ๋๋ฐ(\*๊ผญ ์ด ๋ฐฉ๋ฒ๋ง ์๋๊ฑด ์๋์ง๋ง)  
๊ทธ๊ฑธ ๋ฐ๋ก ์ ์ฅํ๋ ์๊ฐ์ ๋ชป ํ๊ณ  ๊ทธ๋ฅ ๋ฐ๋ณต๋ฌธ์ด ์ข๋ฃ๋ ํ mid๊ฐ์ ์ถ๋ ฅํ๋๋ก ํด๋ฒ๋ฆฐ ๊ฒ ๋๋ฌธ์ ์ ๋๊ฒ ์ฝ์ง์ ํ๋ค.  
๊ฑฐ๊ธฐ๋ค ํฌ์ธํฐ๋ฅผ ์ฎ๊ธฐ๋ ์ง์ ๋ left๋ mid+1๋ก, right๋ mid๋ก ์ก์๋๊ณ  ์์๊ณ ...  
๊ทธ์์ค์ left, right์ ์ด๊ธฐ๊ฐ์ ์ดํฉ๊น์ง ๊ตฌํด๊ฐ๋ฉฐ ์ ๊ฒฝ์ผ๋๋ฐ left์ ๊ฒฝ์ฐ์ ์คํ๋ ค 0์ด ๋  ์๋ ์๋ ์์ด์ด์-_-; ์ธ๋ฐ์๋ ๊ฒ ๊ฐ์ ๊ทธ๋ฅ ๋ค๋ฅธ ํ์ด๋ค์ฒ๋ผ left=1, right=์ต๋ ๋์ ๊ธธ์ด๋ก ์ฌ์ค์ ํ๋ค.  
๋์ด๋์ ๋นํด ์ ๋ต๋ฅ ์ด ์ข ๋ฎ๊ธฐ๋ ํ๋ค๋ง... ์ด์ ๋๋ฉด ์์ง ์๊ณ ์๋ค๊ณ  ํ๊ธฐ๋ ์ด๋ ค์ด๋ฏํ๋ ๋ฌธ์ ๋ค์ ์ข๋ ํ์ด๋ด์ผ๊ฒ ๋ค.  

์ฝ๋๊ฐ ๋๊ฐ์ง๋ฐ ์ฒซ๋ฒ์งธ ์ฝ๋๋ ๋ค๋ฅธ๋ถ๋ค๊ณผ ๋น์ทํ ํ์ด์ด๊ณ   
๋๋ฒ์งธ ์ฝ๋๋ ์ฝ์งํ๋ ๊ณผ์ ์์ ์ฐพ์๋ธ๊ฑด๋ฐ, ๋ฐ๋ก ๊ธธ์ด๊ฐ์ ์ ์ฅํ์ง ์๊ณ  ๋ต์ ๊ตฌํ ๊ฒฝ์ฐ์ด๋ค. ์ฐจ์ด์ ์ ๋ฐ๋ณต๋ฌธ ์กฐ๊ฑด๊ณผ ํฌ์ธํฐ ์ด๋์ง์ , mid๋ฅผ ๋ฐ์ฌ๋ฆผํ์ฌ ๊ณ์ฐํ๋ค๋ ์ ์ด๋ค.  

### ์ฝ๋
```cpp
#include<iostream>

using namespace std;

int main() {
	int k, n;
	int longest = 0, cnt = 0, res = 0;
	int len[10001];
	long long left, mid, right;

	cin >> k >> n;

	for (int i = 0; i < k; i++) {
		cin >> len[i];
		if (longest < len[i]) longest = len[i];
	}

	left = 1;
	right = longest;
	mid = 0;

	while (left <= right) {
		mid = (left + right) / 2;
		cnt = 0;
		for (int i = 0; i < k; i++) {
			cnt += len[i] / mid;
		}
		if (cnt >= n) {
			if (res < mid) res = mid;
			left = mid + 1;
		}
		else right = mid - 1;
	}

	cout << res;
}
```
### ์ฝ๋ #2
```cpp
#include<iostream>

using namespace std;

int main() {
	int k, n;
	int longest = 0, cnt = 0;
	int len[10001];
	long long left, mid, right;

	cin >> k >> n;

	for (int i = 0; i < k; i++) {
		cin >> len[i];
		if (longest < len[i]) longest = len[i];
	}

	left = 1;
	right = longest;
	mid = 0;

	while (left < right) {
		mid = (left + right + 1) / 2;
		cnt = 0;
		for (int i = 0; i < k; i++) {
			cnt += len[i] / mid;
		}
		if (cnt < n) right = (left + right) / 2;
		else left = mid;
	}

	cout << (left + right) / 2;
}
```
<br>

## **3. ์ฉ์ก**
์ง์ง ๋ค ์ง๋๊ณ  ์ธ๋ฐ์์ด ์๊ฐ์ ๋ญ๋นํ๋ค. ์ ๊ธฐ๋ ๋ฏผ๋งํ  ์ ๋๋ฐใใใ์ฉ์ก์ด๋ผ๋ ํค์๋ ๋๋ฌธ์ ์ ๋ง ์ด์ด์๋ ์์...๐คฆ  
์ํผ ์ด ๋ฌธ์ ๋ ํฌํฌ์ธํฐ ๋ฌธ์ ์ด๋ค. ๋ ๊ฐ์ ํฌ์ธํฐ๋ฅผ ์ฌ์ฉํ๋ค๋ ์ ์ ์ด์งํ์๊ณผ ๊ฐ์ง๋ง ์ด์งํ์์ ์ ๋ต ์ธ๋ฑ์ค๋ฅผ ํฅํด ๋ฒ์๋ฅผ ์ ๋ฐ์ฉ ์ขํ๊ฐ๋ ๊ฒ์ด๊ณ , ํฌํฌ์ธํฐ๋ ํ์นธ์ฉ ํ์ผ๋ฉด์ ๊ณ์ฐํ ๊ฐ ์ค ์ ๋ต ์กฐ๊ฑด์ ํด๋นํ๋ ๊ฒฝ์ฐ๋ฅผ ์ฐพ์๋ด๋ ๊ฒ์ด๋ฏ๋ก ๋์ค์ ์ ๋ต์ด ๋์๋๋ผ๋ ๋ ํฌ์ธํฐ๊ฐ ๋๊น์ง ํ์ํ ๋๊น์ง ๊ณ์ํ๋ค.  
๊ทธ๋ ๊ธฐ๋๋ฌธ์ ์ด์งํ์์ ์๊ฐ๋ณต์ก๋๋ O(logn)์ด๊ณ  ํฌํฌ์ธํฐ์ ์๊ฐ๋ณต์ก๋๋ O(n)์ด๋ค. ๊ทธ๋ฆฌ๊ณ  ๋น์ฐํ ๋ง์ด์ง๋ง ์ด์งํ์์ ๋ฐ์ดํฐ๊ฐ ์ ๋ ฌ๋์ด ์์ด์ผ ํ๋ค. ํฌํฌ์ธํฐ๋ ๊ฒฝ์ฐ์ ๋ฐ๋ผ์๋ ์ ๋ ฌ๋์ด์์ง ์์๋ ๋ฌด๋ฐฉํ๋ค.  

์กฐ๊ธ ๋ค๋ฅธ ์ ํ์ ํฌํฌ์ธํฐ ์๊ณ ๋ฆฌ์ฆ ๋ฌธ์ ์ธ [์๋ค์ ํฉ 2](https://www.acmicpc.net/problem/2003) ๋ฌธ์ ๋ฅผ ์ ์ ์ธ๊ธํ์๋ฉด,    
์ฌ๊ธฐ์๋ ๋จผ์  ๋ ํฌ์ธํฐ๋ฅผ ๊ฐ์ ์์น(0๋ฒ์งธ)์ ๋๋ค. ๊ทธ๋ฆฌ๊ณ  ๋ ํฌ์ธํฐ ์ฌ์ด์ ๊ตฌ๊ฐํฉ์ ๊ตฌํ๋๋ฐ, ๋ชจ๋  ๊ฐ์ด ์์ฐ์์ด๊ธฐ ๋๋ฌธ์ ๊ตฌ๊ฐ์ ํฌ๊ธฐ ์ฆ๊ฐ์ด ๊ณง ์ดํฉ์ ์ฆ๊ฐ์ด ๋๋ค.  
๊ทธ๋์ ๋ง์ฝ ํฉ์ด ๊ธฐ์ค๊ฐ๋ณด๋ค ์์ผ๋ฉด ๊ตฌ๊ฐ ํฌ๊ธฐ๋ฅผ ํค์ฐ๊ธฐ ์ํด left๋ ๋๋๊ณ  right๋ฅผ ์ค๋ฅธ์ชฝ์ผ๋ก ํ์นธ ๋ฐ๊ณ , ๊ธฐ์ค๊ฐ๋ณด๋ค ํฌ๋ฉด left๋ฅผ ์ค๋ฅธ์ชฝ์ผ๋ก ํ์นธ ๋ฐ๊ณ  right๋ ๋๋๋ฉด ๋๊ธฐ ๋๋ฌธ์ ๋ฐ์ดํฐ๊ฐ ์ ๋ ฌ๋์ด์์ง ์์๋ ๋๋ค.  

๋ฐ๋ฉด ์ด ์ฉ์ก ๋ฌธ์ ๋ ์ ์ํ์ผ๋ก ์ฃผ์ด์ง๋ ๋ฐ์ดํฐ์์ ๋ ํฌ์ธํฐ๋ง์ ํฉ์ ๋ณด๋ ๊ฒ์ด๊ธฐ ๋๋ฌธ์ ์ ๋ ฌ๋์ด ์์ง ์๋ค๋ฉด ์ด๋์ชฝ ํฌ์ธํฐ๋ฅผ ์ฎ๊ฒจ์ผํ ์ง ๊ฒฐ์ ํ๊ธฐ๊ฐ ์ด๋ ต๋ค.  
๋ฐ๋ผ์ ์ด ๋ฌธ์ ์์๋ ์์ ๋ฐ์ดํฐ๊ฐ ์ค๋ฆ์ฐจ์์ผ๋ก ์ฃผ์ด์ก์ผ๋ฉฐ ๋ ํฌ์ธํฐ์ ์์น๋ ์์ชฝ ๋์์๋ถํฐ ์์ํด ๋ ๊ฐ์ ํฉ์ด ์์๋ผ๋ฉด left๋ฅผ ์ค๋ฅธ์ชฝ์ผ๋ก, ์์๋ผ๋ฉด right๋ฅผ ์ผ์ชฝ์ผ๋ก ์ด๋์์ผ ์ ๋๊ฐ ํฌ๊ธฐ๋ฅผ ์ค์ด๋๋ก ํ๋ค.  
(์ฌ๋ด์ด์ง๋ง... ๋ณ์ ๋ค์ด๋ฐ์ด ์ข ๋ณ๋ก ๊ฒ๊ฐ๋ค.)  

### ์ฝ๋
```cpp
#include<iostream>

using namespace std;

int main() {
	int n, val[100000], left, right, min_left, min_right, min_val;

	cin >> n;
	for (int i = 0; i < n; i++) {
		cin >> val[i];
	}

	left = 0;
	min_left = 0;
	right = n - 1;
	min_right = n - 1;
	min_val = INT32_MAX;

	while (left < right) {
		int total = val[left] + val[right];

		if (abs(total) < abs(min_val)) {
			min_left = left;
			min_right = right;
			min_val = total;
		}
		(total < 0) ? left++ : right--;
	}

	cout << val[min_left] << " " << val[min_right];
}
```
<br>

## **4. ์ธ ์ฉ์ก**
ํต๊ณผ๋ ํ์ง๋ง ์ฒ์์ ์๋ชป ์๊ฐํ๋ ์ฝ๋์ ๋ฌธ์ ์ ์ ์ฐพ๋๋ฐ ์๊ฐ์ด ์ข ๊ฑธ๋ ธ๋ค.  
์ฐ์  ์ ๋ฌธ์ ์ ๋ง์ฐฌ๊ฐ์ง๋ก ์ฌ๊ธฐ์๋ ๋ฐ์ดํฐ๊ฐ ์ ๋ ฌ๋์ด ์์ด์ผ ํ๋๋ฐ ์ด ๋ฌธ์ ์์๋ ๋ฐ์ดํฐ๊ฐ ๋ฌด์์๋ก ์ฃผ์ด์ง๋ฏ๋ก ๋จผ์  algorithm ํค๋์ sort()๋ฅผ ์ฌ์ฉํด ์ ๋ ฌํด์ฃผ์๋ค.  
๊ทธ๋ค์ ์ฒซ๋ฒ์งธ ํฌ์ธํฐ๋ฅผ 0๋ถํฐ n-3๊น์ง ๊ณ ์ ์ผ๋ก ์ ํด๋๊ณ , ๊ฐ ๊ฒฝ์ฐ์ ๋ํด  
```
left์ ์ธ๋ฑ์ค = ์ฒซ๋ฒ์งธ ํฌ์ธํฐ์ ์ธ๋ฑ์ค+1
right์ ์ธ๋ฑ์ค = n-1
```
์์ ์์ํ์ฌ ์ธ ๊ฐ์ ํฉ์ ์ ๋๊ฐ์ด ์ต์๊ฐ ๋๋ ๋ ํฌ์ธํฐ ์์น๋ฅผ ๊ตฌํด์
์ง๊ธ๊น์ง ๊ตฌํ๋ ์ต์๊ฐ๋ณด๋ค ์๋ค๋ฉด ๊ฐฑ์ ํ๋๋ก ํ๋ค.  
๋ ํฌ์ธํฐ์ ์ด๋๋ฐฉ์์ ์ ๋ฌธ์  ๊ทธ๋๋ก์ด๊ณ  ๊ฑฐ๊ธฐ์ ์ฒซ๋ฒ์งธ ๊ฐ๊น์ง ๋ํด์ ๊ฒฐ์ ํ๋ ๋ฐฉ์์ด๋ค.  
์ด๋ ๊ฒ ํ๋ฉด ์ฒซ๋ฒ์งธ ํฌ์ธํฐ์ ๋ํ ์๊ฐ๋ณต์ก๋๊ฐ O(n), ๊ฐ๊ฐ์ ๊ฒฝ์ฐ์ ํฌํฌ์ธํฐ์ ๋ํ ์๊ฐ๋ณต์ก๋๊ฐ O(n)์ด๋ฏ๋ก ์ด ์๊ฐ๋ณต์ก๋๋ O(n^2^)์ด ๋๋ค.  

์ฒ์์ ์๋ชป ์๊ฐํ๋ ์ฝ๋๋ ์ด๋ค ํ๋ฆ์ด์๋๋ฉด  
๋ ํฌ์ธํฐ์ ๋ํด ์ธ ๊ฐ์ ํฉ์ ์ ๋๊ฐ์ด ์ต์๊ฐ ๋๋ ์ธ๋ฒ์งธ ํฌ์ธํฐ์ ์์น๋ฅผ ์ฒ์๋ถํฐ ๋๊น์ง ๋๋ฉด์ ์ฐพ๋๋ก ํ ๊ฒ์ด์๋ค.(๋ฌผ๋ก  ๋ ํฌ์ธํฐ๋ ์ ์ธํ๊ณ )  
๊ทธ๋ฌ๋๊น ์ ๋ฐฉ๋ฒ์์๋ ์ฒซ๋ฒ์งธ ํฌ์ธํฐ๋ฅผ ํ์ํ๋ for๋ฌธ ๋ด์ ํฌํฌ์ธํฐ๋ฅผ ํ์ํ๋ while๋ฌธ์ ๋์๋ค๋ฉด ์ด ๋ฐฉ๋ฒ์ while๋ฌธ ๋ด์ for๋ฌธ์ ๋์๋ค๋ ๊ฑด๋ฐ,  
์ด๋ ๊ฒ ํ๋ฉด ๋ ํฌ์ธํฐ์ ์ด๋ ์์๊ฐ ์ ๋ต์ ํฌํจ์ํค์ง ์๋ ์ชฝ์ผ๋ก ์ด๋ํ๋ ๊ฒฝ์ฐ๊ฐ ๋ฐ์ํด๋ฒ๋ฆฐ๋ค...  
์ข ๋ ์์ธํ ์ค๋ชํ๊ณ  ์ถ์๋ฐ, ์ค์ค๋ก ๋ฐ๋ก๋ฅผ ์ฐพ๊ธฐ๋ ์ฝ์ง ์์๋๋งํผ ์์ง์ ์์ ํ ์ดํดํ์ง ๋ชปํ์ฌ ์ผ๋จ์ ๋ฐฑ์ค์์ ์ฐพ์ ๋ฐ๋ก๋ผ๋ ๋จ๊ฒจ๋๋๋ค.  
(์ถ์ฒ: [https://www.acmicpc.net/board/view/62826](https://www.acmicpc.net/board/view/62826))  
```
์๋ ฅ: 6
13 14 48 -5 18 -96

๋ต: -5 13 14

ํ๋ฆฐ ๋ต: -96 18 48
```
์๋๋ ํต๊ณผํ ์ฝ๋์ด๋ค.  

### ์ฝ๋
```cpp
#include<iostream>
#include<algorithm>

using namespace std;

int main() {
	ios::sync_with_stdio(false);
	cin.tie(NULL);
	cout.tie(NULL);

	int n, left, right, min_f, min_l, min_r, val[5000];
	cin >> n;
	for (int i = 0; i < n; i++) {
		cin >> val[i];
	}

	sort(val, val + n);

	min_f = val[0];
	min_l = val[1];
	min_r = val[n - 1];

	for (int first = 0; first < n-2; first++) {
		left = first + 1;
		right = n - 1;

		while (left < right) {
			long long total = (long long)val[first]+val[left]+val[right];

			if (abs(total) < abs((long long)min_f + min_l + min_r)) {
				min_f = val[first];
				min_l = val[left];
				min_r = val[right];
			}

			(total < 0) ? left++ : right--;
		}
	}

	cout << min_f << " " << min_l << " " << min_r;
}
```
