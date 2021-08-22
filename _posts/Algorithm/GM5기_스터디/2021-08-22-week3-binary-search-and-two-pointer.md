---
title: "Week3 - Binary Search, Two Pointer"
date: 2021-08-22 15:10:00 +0900
categories: [Algorithm, GM5기_스터디]
tags: [baekjoon, binary_search, cpp, study, two_pointer]
---

## **📝 문제 목록**
- [-----] [15641 - SUPER SUPER BINARY SEARCH DELUXE 2.5: THE LEGEND OF THE GOLDEN MAZASSUMNIDA, EPISODE 2: THE MAZWAETL UNIVERSE, PART 2: THE PARALLEL UNIVERSE AND THE LOST MAZASSUMNIDA: GAME OF THE YEAR EDITION](https://www.acmicpc.net/problem/15641)
- [🥈3] [1654 - 랜선 자르기](https://www.acmicpc.net/problem/1654)
- [🥇5] [2467 - 용액](https://www.acmicpc.net/problem/2467)
- [🥇4] [2473 - 세 용액](https://www.acmicpc.net/problem/2473)

---
<br>

## **1. SUPER SUPER BINARY SEARCH DELUXE 2.5: THE LEGEND OF THE GOLDEN MAZASSUMNIDA, EPISODE 2: THE MAZWAETL UNIVERSE, PART 2: THE PARALLEL UNIVERSE AND THE LOST MAZASSUMNIDA: GAME OF THE YEAR EDITION**
구데기컵은 들어만 봤지 문제를 풀어(?)본 건 이번이 처음이다. (문제 이름부터가ㅋㅋ  
내가 직접 이분탐색기가 되어 정답값을 찾는 문제. 1~100 사이의 자연수를 써서 제출하면 정답보다 큰지 작은지 채점 프로그램이 퍼센티지로 알려준다.  

### 코드
```cpp
🙄💭
```
<br>

## **2. 랜선 자르기**
풀어본 유형이라 풀이는 알고 있었으므로 바로 넘어갈 줄 알았는데 어설프게 알고 있던 탓에 포인터 이동 지점을 잘못 잡고... 중요한 부분도 빼먹어버려서 한참 씨름했던 문제다.  
일단 '이분탐색 대상=만들 수 있는 랜선의 최대 길이'로 두고 그 길이로 잘랐다고 가정했을 때 만들어지는 개수를 카운트하면서 left, right로 길이의 범위를 좁혀나가는 것까지는 생각했다.  
그런데 left와 right를 옮기다가 정답을 포함시키지 않는 구간이 되어버리면 mid값이 더 이상 정답이 나올 수 없게 되므로, 그러기 전까지 N개 이상을 만들 수 있는 길이에 한해 그 값을 따로 저장했어야 하는데(\*꼭 이 방법만 있는건 아니지만)  
그걸 따로 저장하는 생각을 못 하고 그냥 반복문이 종료된 후 mid값을 출력하도록 해버린 것 때문에 신나게 삽질을 했다.  
거기다 포인터를 옮기는 지점도 left는 mid+1로, right는 mid로 잡아두고 있었고...  
그와중에 left, right의 초기값은 총합까지 구해가며 신경썼는데 left의 경우엔 오히려 0이 될 수도 있는 식이어서-_-; 쓸데없는 것 같아 그냥 다른 풀이들처럼 left=1, right=최대 랜선길이로 재설정했다.  
난이도에 비해 정답률이 좀 낮기는 했다만... 이정도면 아직 알고있다고 하기는 어려운듯하니 문제들을 좀더 풀어봐야겠다.  

코드가 두가진데 첫번째 코드는 다른분들과 비슷한 풀이이고  
두번째 코드는 삽질하는 과정에서 찾아낸건데, 따로 길이값을 저장하지 않고 답을 구한 경우이다. 차이점은 반복문 조건과 포인터 이동지점, mid를 반올림하여 계산했다는 점이다.  

### 코드
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
### 코드 #2
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

## **3. 용액**
진짜 다 짜놓고 쓸데없이 시간을 낭비했다. 적기도 민망할 정돈데ㅋㅋㅋ용액이라는 키워드 때문에 정말 어이없는 식을...🤦  
암튼 이 문제는 투포인터 문제이다. 두 개의 포인터를 사용한다는 점은 이진탐색과 같지만 이진탐색은 정답 인덱스를 향해 범위를 절반씩 좁혀가는 것이고, 투포인터는 한칸씩 훑으면서 계산한 값 중 정답 조건에 해당하는 경우를 찾아내는 것이므로 도중에 정답이 나왔더라도 두 포인터가 끝까지 탐색할때까지 계속한다.  
그렇기때문에 이진탐색의 시간복잡도는 O(logn)이고 투포인터의 시간복잡도는 O(n)이다. 그리고 당연한 말이지만 이진탐색은 데이터가 정렬되어 있어야 한다. 투포인터는 경우에 따라서는 정렬되어있지 않아도 무방하다.  

조금 다른 유형의 투포인터 알고리즘 문제인 [구간합](https://www.acmicpc.net/problem/2003) 문제를 잠시 언급하자면,    
여기서는 먼저 두 포인터를 같은 위치(0번째)에 둔다. 그리고 두 포인터 사이의 구간합을 구하는데, 모든 값이 자연수이기 때문에 구간의 크기 증감이 곧 총합의 증감이 된다.  
그래서 만약 합이 기준값보다 작으면 구간 크기를 키우기 위해 left는 냅두고 right를 오른쪽으로 한칸 밀고, 기준값보다 크면 left를 오른쪽으로 한칸 밀고 right는 냅두면 되기 때문에 데이터가 정렬되어있지 않아도 된다.  

반면 이 용액 문제는 정수형으로 주어지는 데이터에서 두 포인터만의 합을 보는 것이기 때문에 정렬되어 있지 않다면 어느쪽 포인터를 옮겨야할지 결정하기가 어렵다.  
따라서 이 문제에서는 아예 데이터가 오름차순으로 주어졌으며 두 포인터의 위치는 양쪽 끝에서부터 시작해 두 값의 합이 음수라면 left를 오른쪽으로, 양수라면 right를 왼쪽으로 이동시켜 절대값 크기를 줄이도록 했다.  
(여담이지만... 변수 네이밍이 좀 별론것같다.)  

### 코드
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

## **4. 세 용액**
통과는 했지만 처음에 잘못 생각했던 코드의 문제점을 찾는데 시간이 좀 걸렸다.  
우선 위 문제와 마찬가지로 여기서도 데이터가 정렬되어 있어야 하는데 이 문제에서는 데이터가 무작위로 주어지므로 먼저 algorithm 헤더의 sort()를 사용해 정렬해주었다.  
그다음 첫번째 포인터를 0부터 n-3까지 고정으로 정해놓고, 각 경우에 대해  
```
left의 인덱스 = 세번째 포인터의 인덱스+1
right의 인덱스 = n-1
```
에서 시작하여 세 값의 합의 절대값이 최소가 되는 두 포인터 위치를 구해서
지금까지 구했던 최소값보다 작다면 갱신하도록 했다.  
두 포인터의 이동방식은 위 문제 그대로이고 거기에 첫번째 값까지 더해서 결정하는 방식이다.  
이렇게 하면 첫번째 포인터에 대한 시간복잡도가 O(n), 각각의 경우에 투포인터에 대한 시간복잡도가 O(n)이므로 총 시간복잡도는 O(n^2^)이 된다.  

처음에 잘못 생각했던 코드는 어떤 흐름이었냐면  
두 포인터에 대해 세 값의 합의 절대값이 최소가 되는 세번째 포인터의 위치를 처음부터 끝까지 돌면서 찾도록 한 것이었다.(물론 두 포인터는 제외하고)  
그러니까 위 방법에서는 첫번째 포인터를 탐색하는 for문 내에 투포인터를 탐색하는 while문을 두었다면 이 방법은 while문 내에 for문을 두었다는 건데,  
이렇게 하면 두 포인터의 이동 순서가 정답을 포함시키지 않는 쪽으로 이동하는 경우가 발생해버린다...  
좀 더 자세히 설명하고 싶은데, 스스로 반례를 찾기도 쉽지 않았던만큼 아직은 완전히 이해하지 못하여 일단은 백준에서 찾은 반례라도 남겨놓는다. (출처: https://www.acmicpc.net/board/view/62826)  
```
입력: 6
13 14 48 -5 18 -96

답: -5 13 14

틀린 답: -96 18 48
```
아래는 통과한 코드이다.  

### 코드
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
