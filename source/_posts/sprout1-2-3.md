---
title: "[題解]NEOJ 21 陸行鳥大賽車"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 陸行鳥大賽車
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/21/)
這一題我嘗試了兩個方法
第一個方法：用vector 紀錄名次，但就是會TLE ，原因：需要O(N)找到當前的車車在第幾名，如果遭受攻擊，也要用O(N)來erase vector的元素，結果長這樣：
![](https://i.imgur.com/FNJVrB8.png)
講師說：不要用vector 的erase ，因為他會耗費O(N)的時間

第二個方法：用一個struct 的陣列，裡面包了每一台車的資訊包含他的名次，這樣就可以用O(1)更改每一台車的名次，算是第一個算法的優化

```cpp=
#include <bits/stdc++.h>
using namespace std;
int N,M;
struct node{
    int player_id;
    node *next,*prev;
    node(){
        next = NULL;
        prev = NULL;
    }
};
node *player[100005];

signed main(){
    ios
    cin>>N>>M;
    for(int i=0;i<=N+2;i++){
        player[i] = new node();
        player[i]->player_id = i;
    }
    for(int i=1;i<=N+2;i++){
        player[i]->prev = player[i-1];
        player[i]->next = player[i+1];
    }//初始化設定
    for(int i=0;i<M;i++){
        int a,b;cin>>a>>b;
        if(a==0){
            player[b]->prev->next = player[b]->next;
            player[b]->next->prev = player[b]->prev;
        }//編號b的車車被消滅
        else{
            if(!player[b]->prev->prev)continue;
            int pp,p,n;
            pp = player[b]->prev->prev->player_id;
            p = player[b]->prev->player_id;
            n = player[b]->next->player_id;
            
            player[pp]->next = player[b];
            player[b]->prev = player[pp];
            player[b]->next = player[p];
            player[p]->next = player[n];
            player[n]->prev = player[p];
            player[p]->prev = player[b];
        }//交換名次的部分，不過應該有方法可以更簡單
    }
    stack<int> s;
    int ind = N+1;
    while(player[ind]->prev!=NULL){
        if(ind!=N+1){
            s.push(player[ind]->player_id);
        }
        ind = player[ind]->prev->player_id;
    }
    while(!s.empty()&&s.size()!=1){
        cout<<s.top()<<" ";
        s.pop();
    }
    cout<<s.top()<<endl;
}
```
