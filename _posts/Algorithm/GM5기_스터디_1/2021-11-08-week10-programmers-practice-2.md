---
title: "Week10 - 프로그래머스 연습문제(2)"
date: 2021-11-08 01:25:00 +0900
categories: [Algorithm, GM5기_스터디_1]
tags: [bfs, cpp, dfs, programmers, study]
---

## **📝 문제 목록**
- [Level 2] [타겟 넘버](https://programmers.co.kr/learn/courses/30/lessons/43165)
- [Level 3] [네트워크](https://programmers.co.kr/learn/courses/30/lessons/43162)
- [Level 3] [단어 변환](https://programmers.co.kr/learn/courses/30/lessons/43163)
- [Level 3] [여행경로](https://programmers.co.kr/learn/courses/30/lessons/43164)

---

> 개인적으로 공부한 내용을 작성한 것으로 틀린 부분이 있을 수 있습니다  
틀린 부분이 있다면 지적해주시면 감사하겠습니다🙏  

<br>

## **1. 타겟 넘버**
모든 수를 더하거나 빼서 최종적으로 어떤 수를 만든다 => DFS가 바로 생각나서 그냥 재귀를 이용해 간단히 풀었다..  
풀고나서 다른 사람들의 코드를 한번 봤는데, 그중에 비트를 이용한 풀이가 인상적이어서 같이 적어본다.  
대충 설명해보자면 자리수와 배열 인덱스를 일치시켜서 각 자리수에 대하여 1이면 해당 인덱스의 숫자를 빼고 0이면 더해가는데(안쪽 for문), 그 작업을 0000~1111(예시) 까지 전부 수행한 후(바깥쪽 for문) 결과값이 타겟넘버가 되는지를 판단하는 식이었다.  

### 코드
```cpp
#include <vector>

using namespace std;

int answer = 0;

void DFS(const int& index, const int& sum, const vector<int>& numbers, const int& target){
    if(index == numbers.size()-1){
        if(sum == target) answer++;
        return;
    }
    DFS(index+1, sum+numbers[index+1], numbers, target);
    DFS(index+1, sum-numbers[index+1], numbers, target);
}

int solution(vector<int> numbers, int target) {
    DFS(0, numbers[0], numbers, target);
    DFS(0, -numbers[0], numbers, target);
    return answer;
}
```
<br>

## **2. 네트워크**
그래프 덩어리 개수를 세는 문제. DFS를 사용했다.  
이것도 몇번 풀어봤던 유형이라 금방 풀었다.  

### 코드
```cpp
#include <vector>

using namespace std;

bool is_visited[200];

void DFS(const int& index, const int& n, const vector<vector<int>>& computers){
    is_visited[index]=true;
    
    for(int i=0; i<n; i++){
        if(!is_visited[i] && computers[index][i]==1) DFS(i,n,computers);
    }
}

int solution(int n, vector<vector<int>> computers) {
    int answer = 0;
    
    fill_n(is_visited, n, false);
    for(int i=0; i<n; i++){
        if(!is_visited[i]) answer++;
        DFS(i,n,computers);
    }
    
    return answer;
}
```
<br>

## **3. 단어 변환**
최소 변환 횟수를 구하는 문제이므로 BFS로 풀었다.  
주어진 데이터는 그냥 단어의 집합이기 때문에 변환 가능한지, 즉 연결되어 있는지를 체크하는 함수를 하나 만들어주었다.  
큐에는 인덱스를 저장하도록 했고, 모든 단어에 대하여 각각 최소 변환 횟수를 저장하는 벡터를 만들어서
횟수가 구해지지 않았고(=방문하지 않은) 변환 가능한(=인접한) 단어에 대해 변환 횟수를 저장하고 큐에 들어가게 했다.  

### 코드
```cpp
#include <string>
#include <vector>
#include <queue>

using namespace std;

bool IsConvertible(const string& a, const string& b){
    int count=0;
    for(int i=0; i<a.length(); i++){
        if(a[i]==b[i]) count++;
    }
    return (count==a.length()-1);
}

int solution(string begin, string target, vector<string> words) {
    int answer = 0;
    
    queue<int> q;   // index
    vector<int> count;
    count.assign(words.size(), 0);
    for(int i=0; i<words.size(); i++){
        if(IsConvertible(begin,words[i])){
            count[i]=1;
            q.push(i);
        }
    }
    while(!q.empty()){
        int index = q.front();
        if(words[index]==target) return count[index];
        q.pop();
        
        for(int i=0; i<words.size(); i++){
            if(IsConvertible(words[index], words[i]) && count[i]==0){
                count[i]=count[index]+1;
                q.push(i);
            }
        }
    }
    
    return 0;
}
```
<br>

## **4. 여행경로**
항공권을 모두 사용해야 하며(=모든 간선 방문), 가능한 경로가 2개 이상일 경우 알파벳 순서가 먼저인 경로를 선택해야 한다.  
그러므로 먼저 선택되도록 오름차순 정렬을 해 주었고 그 후 DFS를 수행했다.  
그런데 정답 경로를 어떻게 저장해야 할지 생각하느라 푸는데 시간이 좀 걸렸다. 항공권을 다 쓰지 못한 경로를 걸러낼 방법을 고민하다가 결국 `depth` 를 사용하는 힌트를 얻어 풀게 되었다. `depth` 는 현재 탐색 깊이, 그러니까 티켓을 몇개 사용했는지를 의미한다.  

탐색 과정은 이렇다.  
모든 티켓에 대하여 현재 지점이 출발지이고 아직 사용하지 않은 티켓을 찾았다면, 그것을 사용하고 다음 목적지로 이동(탐색)한다.  
이때 정답 경로는 모든 티켓을 사용한 것이므로, 모든 지점에 대하여 현재의 `depth` 와 `answer` 의 크기(확정된 방문 횟수)를 합한 값이 전체 티켓 수와 같다.  
예를 들어 전체 티켓 수가 3이면 총 4개 지점을 지나쳐야 한다는 건데, 현재 2개의 티켓을 사용했다면 3개 지점을 지나쳤다는 뜻이고  
만약 현재 함수 내에서 재귀함수를 호출한 후에 `answer`에 1개의 지점이 저장되어 있는 상태라면 합계가 맞으므로 지금 지점이 정답 경로에 포함된다는 것이다.  
그러므로 DFS를 수행한 후, 위 조건을 만족하는지를 판단하여 해당되는 지점만을 `answer` 에 넣는다.  
이렇게 하면 항공권을 다 쓰지 못한 경로도 걸러낼 수 있고, 항공권을 다 쓰는 다른 경로도 첫번째 경로가 이미 다 저장된 후 탐색되므로 조건이 맞지 않아 더 이상 `answer` 에 추가되지 않을 것이다.  
그리고 삽입 순서가 역순이므로 반환할때는 순서를 뒤집어 반환해주어야 한다.  

이번 문제는 사실 풀 당시 코드를 너무 지저분하게 짰어서 코드를 한번 고쳤다. 고치고 나니까 풀이도 제대로 정리된듯.. 중복되거나 필요없는 부분이 생기지 않도록 슈도코드를 잘 짜야겠다.  
### 코드
```cpp
#include <string>
#include <vector>
#include <algorithm>
#include <iostream>

using namespace std;

vector<bool> used;
vector<string> answer;

void DFS(const string& here, const vector<vector<string>>& tickets, const int& depth){
    for(int i=0; i<tickets.size(); i++){
        if(tickets[i][0]==here && !used[i]){
            used[i]=true;
            DFS(tickets[i][1], tickets, depth+1);
            used[i]=false;
        }
    }
    if(depth+answer.size()==tickets.size()) answer.push_back(here);
}

vector<string> solution(vector<vector<string>> tickets) {    
    used.assign(tickets.size(), false);
    sort(tickets.begin(), tickets.end());
    
    DFS("ICN", tickets, 0);
    
    reverse(answer.begin(), answer.end());
    return answer;
}
```
