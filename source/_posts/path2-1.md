---
title: "[題解]NEOJ 391 可魚果運輸問題"
date: 2021-1-7
tags: 
    - 最短路徑
    - 題解
categories: 最短路徑題解
mathjax: true
---

### 可魚果運輸問題
<!-- more -->
[題目連結](https://neoj.sprout.tw/problem/391/)
[Dijkstra's Algorithm](https://neoj.sprout.tw/challenge/178079/)
[SPFA](https://neoj.sprout.tw/challenge/178078/)
這一題的來源是NPSC2013的題目，題目大意如下：

> 有n個城市m個運輸方案，每個方案 $P_i$ 會從一個固定的起始城市 $A_i$ 運送東西到另一個固定的終點城市 $B_i$，每運輸一件物品，就必須付 $C_i$ 價錢。若用方案 $P_i$ 運輸了超過 $D_i$ 件物品，多出來的部份每件物品收 $C_i'$ 元。共要運輸 $f$ 物品。
> 求起點 $s$ 到終點 $e$ 的最短路徑。

當運輸總量超過 $D_i$ 件物品，表示可以使用更小的邊權重去計算接下來的費用。這時候我們可以了解到一個性質，如果我把全部的物品全部集中在一條路徑運送，因為送的越多，相對的在費用上就可能產生優惠，比分散多條路徑的運送花費來的小。因為總共要運送 $f$ 件物品，因此會將邊的權重設定為當 $f$ 件物品都經過這一條邊時，所要花費的價格。

有了邊以及價格之後，便可開始進行最短路徑的演算法。因為題目範圍 $N≤100$ ，因此用Dijkstra's 或是 SPFA 都可以做（Bellman-Ford就先不要XD）。

#### Dijkstra's Algorithm

這篇有[一篇論文](http://www-m3.ma.tum.de/foswiki/pub/MN0506/WebHome/dijkstra.pdf)是Dijkstra在針對最短路徑寫的論文（也就是以他名字命名的演算法論文），這篇只有三頁耶！
以下是「演算法導論」這本書中演算法的虛擬碼：

```
DIJKSTRA(G, w, s)
  1  INITIALIZE-SINGLE-SOURCE(G, s)
  2    S <- Ø
  3    Q <- V[G]
  4    while Q ≠ Ø
  5        do u <- EXTRACT-MIN(Q)
  6            S <- S ∪ {u}
  7            for each vertex v ∈ Adj[u]
  8                do RELAX(u,v,w)
```

其中集合 $S$ 在程式碼中代表的是visit，而集合 $Q$ 因為要操作取出最小元素的動作，因此會用priority_queue實現，以heap來進行 $O(\log n)$ 的插入以及取出。

這裡面使用$visit$判斷是否在集合 $S$ 中，但我們可以發現，如果在priority_queue中有兩個點的存在，第一個點處理完被加入集合 $S$ 中之後，第二次再次被拿出來進行處理時就不會有任何相鄰的點再一次被處理，這是因為第一次與第二次更新所使用的 $dis[cur]$ 是一樣的。因此，當一個點已經在visit中（也就是在集合 $s$ 中），我們可以直接換下一個點去執行（不過如果忘了visit也是沒差啦）。

{% note default %}
**複雜度分析**

以下分析一下演算法時間複雜度，總共有兩個時間複雜度，取決於使用何種資料結構來實作。

**1. 時間複雜度 $O(V^2+E)$**
首先是每一個點執行 $V$ 次對每一個相鄰的點進行更新，每一輪執行 $O(V)$ 尋找目前距離 $dis$ 最小的點。總共有 $E$ 條邊需要更新，因此最後要把 $E$ 再加上去。這樣總複雜度就會 $V\times O(V)+O(E) = O(V^2+E)$。此實作方式一般在稠密的圖中會有比較好的表現。
<br>

**2. 時間複雜度 $O((V+E)\log V)$**
這一題在 $O(n)$ 爆搜尋找改用heap去優化，因此複雜度就會是一樣執行V 輪利用 $O(logV)$ 找到距離 $dis$ 最小的點，同時將更新後的點放進heap中，我們知道放入heap的複雜度也是 $O(\log n)$，因此複雜度就會變成 $O(V\log V)+O(E\log V) = O((V+E)\log V)$。此實作方式一般在稀疏的圖中會有比較好的表現，從 $V$ 與 $E$ 的大小估計可以大致發現。
{% endnote %}
以下是程式法的實作，最需要注意的地方是pair的first跟second儲存的東西。在存圖時會使用（點,權重）搭配vector；而在priority_queue中，因為要對權重進行比較，因此會用（權重,點），需要特別注意別搞混了!

{% note success %}
**Priority_Queue實作細節**

預設的priority_queue會pop出目前heap中最大的元素（預設是less<>函數），如果要實作一個min heap 的話要使用greater<>函數，同時第二個傳進函數的參數要用一個vector！

```cpp=
priority_queue <Type, vector<Type>, ComparisonType > min_heap;
priority_queue <pii,vector<pii>,greater<pii>> pq;   
```

{% endnote %}

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define N 105
#define FOR(i,n) for(int i=0;i<n;i++)
#define pii pair<int,int>
using namespace std;

void solve(){
    vector<pii> edge[N];                //存圖
    int n,m,s,e,f;cin>>n>>m>>s>>e>>f;
    int dis[N];fill(dis,dis+N,1e16);
    bool visit[N];                     //城市數n、方案數m、s起、e終、f箱數
    memset(visit,0,sizeof(visit));
    
    for(int i=0;i<m;i++){
        int a,b,c,d,e;cin>>a>>b>>c>>d>>e;
        //一條由a連到b的邊，權重c，流量超過d，則改權重c
        int val = (f>d?c*d+e*(f-d):c*f);
        edge[a].push_back({b,val});
    }
    //Dijkstra
    priority_queue<pii,vector<pii>,greater<pii>> pq;    //(距離,點)
    pq.push({0,s});
    dis[s] = 0;
    
    while(!pq.empty()){
        int cur = pq.top().second;
        pq.pop();
        if(visit[cur])continue;
        
        for(auto i:edge[cur]){
            int next = i.first,weight = i.second;
            
            if(dis[cur]+weight<dis[next]){
                dis[next] = dis[cur]+weight;
                pq.push({dis[next],next});
            }
        }
        visit[cur] = 1;
    }
    cout<<dis[e]<<endl;
}

signed main(){
    ios;
    
    int t;cin>>t;
    while(t--){
        solve();
    }
}
```

#### Shortest Path Faster Algorithm(SPFA)

這是一個使用queue最佳化的Bellman-Ford演算法，基本的使用方式跟BFS幾乎相同，在Bellman-Ford對邊進行更有效率的鬆弛(relaxation)。
以下一樣是「演算法導論」中Bellman-Ford的虛擬碼：

```
BELLMAN-FORD(G,w,s)
  1    INITIALIZE-SINGLE-SOURCE(G,s)
  2    for i <- 1 to |V[G]|-1
  3        do for each edge (u,v)∈ E[G]
  4            do RELAX(u,v,w)
  5    for each edge (u,v)∈ E[G]
  6        do if d[v] > d[u]+w(u,v)
  7            then return FALSE
  8    return TRUE
```

以下是SPFA的虛擬碼，如果節點有被實際鬆弛過，且節點不在queue中（同樣元素在queue中出現兩次沒有意義，因為只要確保有在queue中等會會被更新到就好，到它的最短距離不會被改變），便將此節點推入queue中。裡面存的是帶進行鬆弛的節點們，也就是被剛剛更新過的節點。我們可以利用$visit[ ]$來 $O(1)$ 判斷節點是否有在queue中！

有進行判斷是否在queue中的（比較省空間一點）：
![](https://i.imgur.com/vdtJWR2.png)
沒有進行判斷的，有被更新過就塞進去：
![](https://i.imgur.com/fsorAoE.png)

```
Shortest-Path-Faster-Algorithm(G, s)
  1    for each vertex v ≠ s in V(G)
  2        d(v) := ∞
  3    d(s) := 0
  4    offer s into Q
  5    while Q is not empty
  6        u := poll Q
  7        for each edge (u, v) in E(G)
  8            if d(u) + w(u, v) < d(v) then
  9                d(v) := d(u) + w(u, v)
 10                if v is not in Q then
 11                    offer v into Q
```

SPFA有一個問題，他可以被出題目的人設計測資去卡這種算法，使時間複雜度變成糟糕的$O(V^2)$，因此一般來說會實作Dijkstra而非SPFA！

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define N 105
#define FOR(i,n) for(int i=0;i<n;i++)
#define pii pair<int,int>
using namespace std;

void solve(){
    vector<pii> edge[N];                //存圖
    int n,m,s,e,f;cin>>n>>m>>s>>e>>f;
    int dis[N];fill(dis,dis+N,1e16);
    bool visit[N];                     //城市數n、方案數m、s起、e終、f箱數
    memset(visit,0,sizeof(visit));
    
    for(int i=0;i<m;i++){
        int a,b,c,d,e;cin>>a>>b>>c>>d>>e;
        //一條由a連到b的邊，權重c，流量超過d，則改權重c
        int val = (f>d?c*d+e*(f-d):c*f);
        edge[a].push_back({b,val});
    }
    //SPFA
    
    queue<int> que;
    que.push(s);
    dis[s] = 0;
    visit[s] = 1;
    
    while(!que.empty()){
        int cur = que.front();
        que.pop();
        visit[cur] = 0;                //pop出來將狀態改成不在queue中
        for(auto i:edge[cur]){
            int next = i.first,weight = i.second;
            if(dis[next] > dis[cur]+weight){
                dis[next] = dis[cur]+weight;
                if(!visit[next])que.push(next);
            }
        }
    }
    cout<<dis[e]<<endl;
}

signed main(){
    ios;
    
    int t;cin>>t;
    while(t--){
        solve();
    }
}
```
