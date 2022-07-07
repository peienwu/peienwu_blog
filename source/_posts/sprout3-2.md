---
title: "[題解]TOJ 1101 哪裡有卦，哪裡就有源"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 哪裡有卦，哪裡就有源(TOJ 1101)
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/1101/)
明顯的二分圖水題喔～
用 dfs 把經過的點標出黑白，過程中如果遇到問題就表示非二分圖了

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define int long long
using namespace std;
int n;

bool visit[100005],color[100005];
vector<int> edge[100005];

bool dfs(int id, bool col){
    visit[id] = 1;
    color[id] = col;
    int len = edge[id].size();
    for(int i=0;i<len;i++){
        int temp = edge[id][i];
        if(visit[temp]){
            if(color[temp]==col)return 0;
            else continue;
        }
        if(!dfs(temp, !col))return 0;
    }
    return 1;
}
signed main(){
    ios;
    while(cin>>n && n){
        int m;cin>>m;
        for(int i=0;i<n;i++)edge[i].clear();
        memset(visit, 0, sizeof(visit));
        memset(color, 0, sizeof(color));
        while(m--){
            int a,b;cin>>a>>b;
            edge[a].push_back(b);
            edge[b].push_back(a);
        }
        bool f = 1;
        for(int i=0;i<n;i++){
            if(!visit[i]){
                if(!dfs(i, 0)){
                    f = 0;
                    break;
                }
            }
        }
        if(f)cout<<"NORMAL."<<endl;
        else cout<<"RAINBOW."<<endl;
    }
}
```
