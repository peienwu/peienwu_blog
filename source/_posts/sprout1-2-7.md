---
title: "[題解]NEOJ 293 樹重心"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 樹重心
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/293/)
超愛這一題，竟然可以用DFS 跑一次O(N)把所有的點的資訊全部求出來
透過簡單的運算把看起來很複雜的題目很優美的解出來！

```cpp=
#include <bits/stdc++.h>
using namespace std;
int t;

vector<int> Edge[100005];
bool visit[100005];

struct Node{
    int sum;//子節點總數（扣掉算父節點）
    int maxn;//子數最大的那一個
}node[100005];

int dfs(int id){
    visit[id] = true;
    int len = Edge[id].size();
    for(int i=0;i<len;i++){
        int temp = Edge[id][i];
        if(visit[temp]==1)continue;
        int t = dfs(temp);
        node[id].sum += t;
        node[id].maxn = max(node[id].maxn,t);
    }
    return node[id].sum+1;
}

signed main(){
    cin>>t;
    while(t--){
        int n;cin>>n;
        memset(visit, 0, sizeof(visit));
        
        for(int i=0;i<n;i++){//initialize
            Edge[i].clear();
            node[i].maxn = node[i].sum = 0;
        }
        for(int i=0;i<n-1;i++){
            int a,b;cin>>a>>b;
            Edge[a].push_back(b);
            Edge[b].push_back(a);
        }
        dfs(0);
        int ans = n,index = 0;
        for(int i=0;i<n;i++){
            int temp = max(node[i].maxn,n-node[i].sum-1);//子與父求慘度
            if(temp<ans){
                index = i;
                ans = temp;
            }
        }
        cout<<index<<endl;
    }
}
```
