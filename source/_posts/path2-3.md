---
title: "[題解]NEOJ 431 江神與他的小火車"
date: 2021-1-7
tags: 
    - 最短路徑
    - 題解
categories: 最短路徑題解
mathjax: true
---


### 江神與他的小火車
<!-- more -->
[題目連結](https://neoj.sprout.tw/problem/431/)

> 題目大意
> 已知有N個點以及M條邊，總共有Q筆詢問，每筆詢問有$\delta(a,b)$，表示新增一條由 $a$ 指向 $b$ 的邊（其權重為1），對每一筆詢問輸出從點 $1$ 到點 $N$ 的最短距離。

![](https://i.imgur.com/f96GUmd.jpg)

我們知道如果有用新加進來的邊 $\delta(a,b)$，則路徑可以被拆解成 $\delta(1,a)+\delta(a,b)+\delta(b,n)$。其中，我們可以用Dijkstra 一次尋找1到任何點的最短距離，那要如何處理路徑 $\delta(b,n)$ 呢？這就是**單一目的最短距離問題**，我們可以將所有的邊逆序，將終點做一次Dijkstra尋找最短路，這時候我們就可以用 $O(1)$ 的時間回答每一筆詢問，總時間複雜度：預處理$O((V+E)\log V)$，詢問 $O(Q)$。

{% note info %}
**測資小問題**
有一筆測資如下，藍線是給定的路徑與權重，紅色是新加入的邊，如果按照上面 $\delta(1,a)+\delta(a,b)+\delta(b,n)$ 我們可以計算出從起點到終點的最短距離為：$2+1+4 = 7$，很明顯兩點之間的最短距離是 $5$ ，但因為原本作法會經過一條$\delta (B,C)$ 的重邊，造成算出來的距離是比較大的！這時候我們只要對最短路徑長度取min即可，也就是說在這種情況加入這一條邊並不會影響最短路徑的長度！

> 測資（1,2,3分別對應A,B,C）：
> 6 5 1
> 1 2 1
> 2 3 1
> 3 4 1
> 4 5 1
> 5 6 1
> 3 2

![](https://i.imgur.com/Uocw0hc.png)
{% endnote %}
接下來是程式碼，之後如果遇到最短路徑的題目，一般來說會避免使用SPFA（除非有負邊），為了要避免最糟的時間複雜度，因此會使用Dijkstra來處理最短路徑問題！

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define N 200005
#define FOR(i,n) for(int i=0;i<n;i++)
#define pii pair<int,int>
using namespace std;
int n,m,q;
bool visit[N];
vector<pii> edge[2][N];
//edge[0]->normal,edge[1]->opposite

vector<int> Dijkstra(int start,int end,bool is_nor){
    memset(visit,0,sizeof(visit));
    vector<int> dis(n+2,1e16);
    
    priority_queue<pii,vector<pii>,greater<pii>> pq;
    
    dis[start] = 0;
    pq.push({0,start});
    
    while(!pq.empty()){
        int cur = pq.top().second;
        pq.pop();
        if(visit[cur])continue;
        
        visit[cur] = 1;
        for(auto i : edge[is_nor][cur]){
            int next = i.first,weight = i.second;
            if(dis[next] > dis[cur] + weight){
                dis[next] = dis[cur] + weight;
                pq.push({dis[next],next});
            }
        }
    }
    return dis;
}

void solve(){
    
    cin>>n>>m>>q;
    for(int i=0;i<m;i++){
        int a,b,w;cin>>a>>b>>w;
        edge[0][a].push_back({b,w});
        edge[1][b].push_back({a,w});
    }
    
    vector<int> normal,opposite;
    normal = Dijkstra(1,n,0);
    opposite = Dijkstra(n,1,1);
    
    while(q--){
        int a,b;cin>>a>>b;
        cout<<min(normal[a]+opposite[b]+1,normal[n])<<endl;
    }
}

signed main(){
    Orz;
    int t;t = 1;
    while(t--){
        solve();
    }
}
```
