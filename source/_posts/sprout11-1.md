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
在一張圖中找割點（定義：拔掉它整張圖就不連通了）
作法：如果用暴力，可以每一個點拔掉，做一次DFS判斷是否聯通（效率太差）

效率更高的就是**Trajan 演算法**找AP(articulation point)

{% note info %}
**Tarjan's algorithm 找 AP**
邊的種類可以分成：Tree edge,Back edge,Forward edge,Cross edge，其中無向圖中只會有樹邊跟回邊（按照無向邊DFS的結果，Foward edge 都會變成子孫的back edge, cross edge 會變成樹邊）

維護一個low函數，代表不經過父節點能到的最小時間戳記（進入）的節點，lv函數為當前節點的時間戳記。一個點是不是割點，只要他的任意子節點的low函數大於等於自己的時間戳記編號，那把這個點拔掉，他小孩就走不到祖先了（如果走得到祖先，對於這一棵子樹，拔掉當前節點就可利用此邊繼續連通），所以他就是割點。
更新：$low[now] = min(low[now],low[next])$分別為利用子孫或靠自己
{% endnote %}

必須要注意的是，割點判斷時要把root的特例獨立判斷。其實root反而比較簡單，如果root有超過一個子樹，代表拔掉root以後會分裂成以每個子樹為單位的連通塊

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0);
#define int long long
#define N 1000001
using namespace std;
int n,m,low[N],lv[N],es=1,root,son_cnt = 0;
bool visit[N],ans[N];
vector<int> edge[N];
//low函數為不經過父節點能到的最小時間戳記,lv為當前進入時間戳記

void DFS(int now,int father){
    visit[now] = 1;             //將此點設為已拜訪
    low[now] = lv[now] = es++;  //進入的時間戳記
    
    int len = edge[now].size();
    for(int i=0;i<len;i++){     //拜訪每一個子孫
        int next = edge[now][i];
        if(now==root && !visit[next])son_cnt++;//計算root小孩（處理特例）
        if(!visit[next]){       //排除走到祖先的情況
            DFS(next, now);
            if(low[next]>=lv[now] && now!=root)ans[now]=1;
            //無法透過小孩到達比自己淺的節點，將now設為AP
        }
        if(next!=father)low[now] = min(low[now],low[next]);
        //排除指向父親的情況，如果經過父親，拔掉就不能往更上面去
    }
}

signed main(){
    ios;
    cin>>n>>m;
    memset(lv, 0, sizeof(lv));
    memset(low, 0, sizeof(low));
    memset(visit, 0, sizeof(visit));
    memset(ans,0,sizeof(ans));
    
    for(int i=0;i<m;i++){
        int x,y;cin>>x>>y;
        edge[x].push_back(y);
        edge[y].push_back(x);
    }
    for(int i=0;i<n;i++){
        if(!visit[i]){
            root = i;
            son_cnt = 0;
            DFS(i, i);
            if(son_cnt>1)ans[root] = 1;
        }
    }
    for(int i=0;i<n;i++)if(ans[i])cout<<i<<endl;
}
```
