---
title: "Week11 - 프로그래머스 연습문제(3)"
date: 2021-11-17 02:15:00 +0900
categories: [Algorithm, GM5기_스터디_1]
tags: [bfs, binary_search, cpp, graph, programmers, study]
---

## **📝 문제 목록**
- [Level 3] [입국심사](https://programmers.co.kr/learn/courses/30/lessons/43238)
- [Level 3] [가장 먼 노드](https://programmers.co.kr/learn/courses/30/lessons/49189)

---

> 개인적으로 공부한 내용을 작성한 것으로 틀린 부분이 있을 수 있습니다  
틀린 부분이 있다면 지적해주시면 감사하겠습니다🙏  

<br>

## **1. 입국심사**
유형별로 푸느라 어쩔 수 없었지만, 문제 유형을 알고 푸는건 역시 별로 좋지 않은 것 같다. 이 문제도 사실 모르고 봤더라면 아래의 풀이방식으로 접근하는데 더 많은 시간이 걸렸을 것이다. 왜냐면 이분탐색 키워드를 의식하지 않고서 처음 문제를 읽고는 '심사대를 어떻게 골라야 하지?'라고 일차원적으로 접근하려 했기 때문이다. 그다음 금방 풀이가 떠올랐지만 아무래도 이분탐색인걸 알았기 때문에 그렇지 않았나 싶었다.  
비슷한 문제들을 예전에 몇번 풀었는데도 오랜만에 풀었다고 왜 또 새롭게 느껴지는지.. 그래도 풀 때마다 조금씩 감을 잡아가고 있는듯하고 점점 알고리즘도 익숙해져서 풀어봤던 경험들이 의미가 없거나 그렇진 않다.  

아무튼 문제에서 원하는 것은 n명을 심사하는 데 드는 소요시간의 최솟값인데, 이를 구하기 위한 접근과정을 정리해보면 이렇다.  
어떤 i번째 심사대의 심사시간을 times[i]라고 하면, T시간동안 그 심사대에서 심사가능한 인원 수는 T/times[i] 이다. 즉 T시간동안 모든 심사대에서 심사가능한 전체 인원 수는 각 심사대의 T/times[i]를 모두 더한 값이다. T가 너무 작으면 그 값이 n을 넘지 못할 수도 있고, T가 너무 크면 그 값이 n을 한참 넘을 수도 있다.  
그렇다면 그 값이 n을 처음 넘기는 시점은 언제인가? 다시말해, n명 심사가 가능하려면 T가 최소한 얼마보다 커야 되는가?  

이때 최솟값을 찾기 위해 T시간을 일일이 대입해보자니 너무 오래 걸리기 때문에 정답이 속한 범위를 반씩 줄여나가는 방법을 사용하는 것이다. 어떤 T에서 n명 심사가 불가능하면 T보다 작은 모든 시간 역시 불가능하다는 뜻이고 그 반대도 마찬가지니까 굳이 죄다 구해볼 필요가 없는거다.  

### 코드
```cpp
#include <vector>

using namespace std;

long long solution(int n, vector<int> times) {
    long long answer = 0;
    long long left = 1;
    long long right = 1'000'000'000'000'000'000;
    
    while(left<right){
        long long mid = (left+right)/2;
        long long headcount=0;
        for(const auto& t : times){
            headcount+=(mid/t);
        }
        if(n<=headcount){
            answer=mid;
            right=mid;
        }
        else left=mid+1;
    }
    
    return answer;
}
```
<br>

## **2. 가장 먼 노드**
단순한 단일 출발 최단경로 문제로, BFS를 사용해 각 노드의 최단경로를 구한 후 가장 먼 노드의 개수를 세어주었다.  
입력으로 주어진 간선정보가 담긴 2차원  배열 `edge` 로 어떤 노드 `here`  에 연결된 모든 노드를 확인하면서(이때 간선이 양방향이므로 반대방향도 체크)  
연결된 노드 중 아직 최단경로를 구하지 않은 노드에 대하여 최단경로를 저장하고 큐에 넣었다.  
그러면서 동시에 가장 먼 거리 `farthest` 를 갱신하고 반복문이 끝난 후에는 각각의 최단경로와 비교하여 가장 먼 거리에 해당하면 카운트를 올려주었다.  

### 코드
```cpp
#include <queue>
#include <vector>

using namespace std;

int solution(int n, vector<vector<int>> edge) {
    int answer = 0;
    int farthest = 0;
    vector<int> count;
    queue<int> q;

    count.assign(n+1,-1);
    count[1]=0;
    q.push(1);
    while(!q.empty()){
        int here = q.front();
        q.pop();

        for(const auto& e : edge){
            if(e[0]==here && count[e[1]]==-1){
                count[e[1]]=count[e[0]]+1;
                farthest=count[e[0]]+1;
                q.push(e[1]);
            }
            else if(e[1]==here && count[e[0]]==-1){
                count[e[0]]=count[e[1]]+1;
                farthest=count[e[1]]+1;
                q.push(e[0]);
            }
        }
    }

    for(const auto& c : count){
        if(c==farthest) answer++;
    }

    return answer;
}
```
