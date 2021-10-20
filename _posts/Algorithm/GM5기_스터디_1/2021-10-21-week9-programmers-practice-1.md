---
title: "Week9 - 프로그래머스 연습문제(1)"
date: 2021-10-21 04:42:00 +0900
categories: [Algorithm, GM5기_스터디_1]
tags: [cpp, dfs, greedy, programmers, study]
---

## **📝 문제 목록**
- [Level 1] [K번째수](https://programmers.co.kr/learn/courses/30/lessons/42748)
- [Level 2] [소수 찾기](https://programmers.co.kr/learn/courses/30/lessons/42839)
- [Level 2] [조이스틱](https://programmers.co.kr/learn/courses/30/lessons/42860)

---

> 개인적으로 공부한 내용을 작성한 것으로 틀린 부분이 있을 수 있습니다  
틀린 부분이 있다면 지적해주시면 감사하겠습니다🙏  

<br>

## **1. K번째수**
정렬에 알고리즘 헤더를 썼기에 딱히 설명할 부분은 없다. 그냥 문제 설명 그대로 i번째부터 j번째까지 자르고 정렬해서 k번째에 있는 수를 모아 반환했을 뿐... assign 범위만 좀 주의해줬다.  

### 코드
```cpp
#include <string>
#include <vector>
#include <algorithm>

using namespace std;

vector<int> solution(vector<int> array, vector<vector<int>> commands) {
    vector<int> answer;
    vector<int> c_arr;
    
    for(auto &c : commands){
        c_arr.clear();
        c_arr.assign(array.begin()+(c[0]-1), array.begin()+(c[1]));
        sort(c_arr.begin(), c_arr.end());
        answer.push_back(c_arr[c[2]-1]);
    }
    
    return answer;
}
```
<br>

## **2. 소수 찾기**
종이조각으로 모든 숫자 조합 만들기(순열), 소수 찾기 이렇게 2가지를 해결해야 하는 문제.  
소수찾기는 하도 풀어서 바로 에라토스테네스의 체가 떠올랐고...오히려 숫자 조합을 만드는 코드에 시간을 더 썼다.  
나는 DFS로 구현했는데, 알고리즘 헤더에 순열을 구하는 함수가 있어서(next_permutation()) 이를 사용하면 쉽게 구할 수 있다.  

근데 그보다 내가 쓴거긴 하지만 일단 빨리 푼답시고 변수명을 신경쓰지 않은게 좀 거슬린다... 특히 종이 조각 문자열이 numbers인데 만든 숫자 문자열을 number로 써놓은건;; 스스로에게도 혼란을 줄 수 있으니 어느정도는 신경써서 이름짓자.  

### 코드
```cpp
#include <string>
#include <vector>
#include <set>
#include <cmath>

using namespace std;

set<int> s_num;
int max_num=0;

void makeNumber(bool isVisited[], string numbers, string number){
    if(number.length()==numbers.length()) return;
    
    string new_str;
    int new_num;
    
    for(int i=0; i<numbers.length(); i++){
        if(!isVisited[i]){
            new_str = number+numbers[i];
            new_num = stoi(new_str);
            
            if(max_num<new_num) max_num=new_num;
            s_num.insert(new_num);
            
            isVisited[i]=true;
            makeNumber(isVisited, numbers, new_str);
            isVisited[i]=false;
        }
    }
}

int solution(string numbers) {
    int answer = 0;
    
    bool isVisited[7];
    fill_n(isVisited, numbers.length(), false);
    makeNumber(isVisited, numbers, "");
    
    vector<bool> isPrime;
    isPrime.assign(max_num+1, true);
    isPrime[0]=false;
    isPrime[1]=false;
    
    for(int i=2; i<=sqrt(max_num); i++){
        int mul=2;
        while(i*mul<=max_num) isPrime[i*mul++]=false;
    }
    
    for(auto &s : s_num){
        if(isPrime[s]) answer++;
    }
    
    return answer;
}
```
<br>

## **3. 조이스틱**
사실상 이번 포스팅은 이걸 리뷰하기 위해서인듯ㅋㅋ 다른 풀이도 검색은 해봤지만 어쨌든 혼자 생각하고 정리한 내용들이라 혹시 틀렸거나 놓친 부분이 있을까 걱정도 되는데, 일단 당시 생각을 기록해둔다.  
이 문제는 어떤 문자열을 완성시키기 위한 조이스틱 조작 횟수의 최솟값을 구하는 문제인데, 횟수 계산은 두 부분으로 나누어 생각할 수 있다.  
1. 조이스틱을 상/하로 조작: 현재 커서 위치의 알파벳을 바꾸는 데 쓴 횟수
2. 조이스틱을 좌/우로 조작: 커서 위치를 이동하는 데 쓴 횟수

1번은 쉽다. A\~M까지는 위쪽으로 이동하는 게 빠르고, N은 어디로 이동하든 같으며, O\~Z까지는 아래쪽으로 이동하는 게 빠르기 때문에 그에 맞게 계산해주면 된다.  

문제는 2번이다. 좌/우 어느쪽으로 움직이느냐에 따라 불필요한 이동이 생길 수도 있기 때문에 최적의 방향을 선택해야 하는데, 문제에 문제가 좀 있다. ~~문제탈트붕괴..~~  
바로 '항상 최솟값을 구하지는 못할수도 있는' 알고리즘에 대해 정답 처리한다는 점이다.  

결론부터 말하자면 이 문제는 그리디로 해결할 수 없다. 반례로 이런 경우가 있다.  
```
B A B A A A A A B A B
```

이 문자열을 만드는 최소 경로는 이렇다.  
```
① 처음 위치의 B를 만들고(0+1)
② 오른쪽으로 두번 이동해서 3번째의 B를 만들고(2+1)
③ 왼쪽으로 세번 이동해서 오른쪽 끝에 있는 B를 만들고(3+1)
④ 왼쪽으로 두번 이동해서 9번째 B를 만든다(2+1)
```
이렇게 하면 총 11회가 된다.  

그러나 그리디로 풀 경우 '현재 위치에서 바꾸어야 할 문자가 더 가까운 쪽'을 선택하는데, 그러면 만드는 경로는 다음과 같다.  
```
① 처음 위치의 B를 만들고(0+1)
② 왼쪽으로 한번 이동해서 오른쪽 끝에 있는 B를 만들고(1+1)
③ 왼쪽으로 두번 이동해서 9번째 B를 만들고(2+1)
④ 오른쪽으로 다섯번 이동해서 3번째 B를 만든다(5+1) (※양쪽 끝에서 이동가능하다고 가정)
```
이렇게 하면 총 12회가 되어버린다.  

그리고 또 하나..  
지문에서 왼쪽으로 이동할 시에는 '첫 번째 위치에서 왼쪽으로 이동하면 마지막 문자에 커서가 위치한다'고 나와있지만 반대로 '마지막 위치에서 커서를 오른쪽으로 이동하면 맨 왼쪽으로 가는가?' 에 대해서는 적혀있지 않다.  
적혀있지 않으면 이동 불가능하다고 판단하는 쪽이 맞지 않나 싶은데, 또 원문에는 다음과 같이 가능하다고 명시되어 있다.  
>...The selection also wraps around, so moving left when the first letter is selected will select the last letter ***and vice versa***. ...

솔직히 말하면 나는 원문도 안 봤으면서 당연히 반대방향도 가능한걸로 멋대로 판단하고 풀었다..=_=;;  
암튼 그게 왜 문제가 되냐면, '바꿀 문자가 좌/우 같은 거리에 있을 때 어디로 이동하는게 횟수가 더 적게 나오는가?' 에 대한 답이 달라질 수 있기 때문이다.  
```
[예시 1] A B A A A A A B A B
[예시 2] A B A B A A A A A B
```

만약 문제에 쓰여진 왼쪽 방향만 이동이 가능하다고 하면  
바꿀 문자가 좌/우 같은 거리에 위치해 있을 때 무조건 오른쪽으로 이동하는 쪽이 더 적은 횟수를 낸다.  
그러나 양쪽 방향 모두 이동이 가능하게끔 구현한 상태에서, 바꿀 문자가 좌/우 같은 거리에 위치해 있으면 케이스에 따라 달라진다.  
[예시 1] 에서는 오른쪽으로 이동했을 때가 더 적게 나오고  
[예시 2] 에서는 왼쪽으로 이동했을 때가 더 적게 나오게 된다.  
그래서 사실 양쪽 끝 이동을 가능하게 하면 어느쪽도 항상 최솟값을 구하지는 못하게 되는데,  
현재(2021년 10월 21일) 기준 테스트케이스는 그리디 알고리즘으로, 양쪽 이동 가능하게, 그리고 거리가 같을 때 오른쪽으로 이동하도록 구현했을 경우 정답을 받을 수 있도록 되어 있다.  
문제유형도 그리디로 분류되어 있고.  
그리디 문제가 되려면... 지문이 수정되든지 아니면 그냥 유형을 정정하고 그리디로 풀리지 않도록 테스트케이스가 추가되어야 할 것 같다.  

아무튼 아래는 위에 설명한대로 구현한, 정답으로 채점된 그리디 코드다.  
실제로는 어떤 경우에도 최솟값이 나오도록 하려면 다른 방법으로 구현해야 한다.ㅠㅠ  

### 코드
```cpp
#include <string>
#include <vector>

using namespace std;

int Counting(string name, int dir, int cur_idx){
    int index=cur_idx;
    int cnt=0;
    
    do{
        cnt++;
        index+=dir;
        if(index<0 || index>=name.length()) index+= -dir * name.length();
    }while(name[index]=='A' && index!=cur_idx);
    
    return cnt;
}

int solution(string name) {
    int answer = 0;
    int cnt = 0;
    
    for(auto &c : name) if(c!='A') cnt++;
    
    int cur_idx = 0;
    while(0 <= cnt){
        if(name[cur_idx]!='A'){
            answer+= ((name[cur_idx]<'N')? name[cur_idx]-'A': 'Z'-name[cur_idx]+1);
            name[cur_idx]='A';
            cnt--;
        }
        if(cnt==0) break;
        
        int left_cnt, right_cnt;
        left_cnt = Counting(name, -1, cur_idx);
        right_cnt = Counting(name, 1, cur_idx);
        
        if(left_cnt<right_cnt){
            cur_idx-=left_cnt;
            if(cur_idx<0) cur_idx+=name.length();
            answer+=left_cnt;
        }
        else{
            cur_idx+=right_cnt;
            if(cur_idx>=name.length()) cur_idx-=name.length();
            answer+=right_cnt;
        }
    }
    
    return answer;
}
```
