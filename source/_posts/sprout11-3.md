---
title: "[題解]NEOJ 183 高棕櫚傳遞鏈"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 高棕櫚傳遞鏈
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/183/)
前兩題找割點，這一題是找橋，跟找割點的方法幾乎一樣，而且對於邊還不需要討論root的情況（root變成不是特例），然後判斷割點的 >= 變成 >，原因可以透過畫圖理解（把點拔掉跟把邊拔掉的差別），就可以實作了！
題目有一個特別的要求，按照給邊的順序進行輸出，那可以搭配set來快速查看某一元素是否在集合內$O(logN)$，接著就按照給定的條件來輸出

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0);
#define int long long
#define N 1000001
using namespace std;
int n,m,lv[N],low[N],timestamp = 1;
bool visit[N];
vector<int> edge[N];
vector<pair<int, int>> ans;
set<pair<int, int>>s;

void DFS(int now,int father){
    lv[now] = low[now] = timestamp++;
    visit[now] = 1;
    
    int len = edge[now].size();
    for(int i=0;i<len;i++){
        int next = edge[now][i];
        
        if(!visit[next]){
            DFS(next, now);
            if(low[next] > lv[now]){
                if(next<now)s.insert(make_pair(next,now));
                else s.insert(make_pair(now, next));
            }
        }
        if(next!=father)low[now] = min(low[now],low[next]);
    }
}

signed main(){
    ios;
    cin>>n>>m;
    for(int i=0;i<m;i++){
        int x,y;cin>>x>>y;
        edge[x].push_back(y);
        edge[y].push_back(x);
        ans.push_back(make_pair(x, y));
    }
    memset(visit, 0, sizeof(visit));
    
    for(int i=1;i<=n;i++){
        if(!visit[i]){
            DFS(i, i);
        }
    }
    for(int i=0;i<m;i++){
        pair<int, int> temp = ans[i];
        if(s.find(temp)!=s.end()){
            cout<<temp.first<<" "<<temp.second<<endl;
        }
    }
}
```
