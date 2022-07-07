---
title: "[題解]NEOJ 165 陣線推進"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 陣線推進
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/165/)
拓墣排序題，兩種實作方式，第一是BFS變形實作（queue實作），被pop出來的順序就是topological sort
第二種就是DFS搭配時間戳記，最晚離開的放在最前面

這一題是把入度為0的節點先push進priority_queue（按照字典序），當一個點處理過之後，入度變成0的所有它指向的點在push進queue裡面
拓墣排序可以用在DAG的判定以及解決具有依賴關係的問題

```cpp=
#include <bits/stdc++.h>
#define N 100001
using namespace std;
int t,n,m,deg[N];//入度
int ans[N],ind = 0;

signed main(){
    cin>>t;
    while(t--){
        memset(deg,0,sizeof(deg));
        memset(ans, 0, sizeof(ans));
        ind = 0;
        vector<int> edge[N];
        cin>>n>>m;
        for(int i=0;i<m;i++){
            int a,b;cin>>a>>b;
            edge[a].push_back(b);
            deg[b]++;
        }
        priority_queue<int,vector<int>,greater<int> > qq;//priority_queue取代queue
        for(int i=0;i<n;i++)if(deg[i]==0)qq.push(i);
        while(!qq.empty()){
            int cur = qq.top();
            qq.pop();
            ans[ind++] = cur;
            int len = edge[cur].size();
            for(int i=0;i<len;i++){
                deg[edge[cur][i]]--;
                if(deg[edge[cur][i]]==0)
                    qq.push(edge[cur][i]);
            }
        }
        if(ind==n){
            cout<<ans[0];
            for(int i=1;i<n;i++)cout<<" "<<ans[i];
            cout<<endl;
        }
        else cout<<"QAQ"<<endl;
    }
}
```
