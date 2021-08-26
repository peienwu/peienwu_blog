---
title: 2020資訊之芽—最短路徑(Shortest Path)
date: 2021-8-21
tags: 
    - 2020資訊之芽
    - 2021暑期筆記
categories: 暑期算法筆記
mathjax: true
---

今年是2021，資芽的二階主題跟2020上的有很多的差別，因此會利用暑假把2020的東西也補一補！
## 課程內容

### 路徑與權重
* $G=(V,E)$
* 尋找最短路徑權重和最小
* 無帶權：BFS直接做(or DFS)
* 有帶權最短路徑
<!-- more -->

### Floyd-Warshall：全點對最短路徑(All Pairs)
- 不支援負環
- 想法：DP轉移（三個迴圈中點、起點、終點依序鬆弛）
-  $d[i][j] = mid(d[i][j],d[i][k]+d[I][k]+d[k][j])$
- 如果改寫成定義 $dp[k][i][j]$ 為點 $i$ 走到點 $j$ ，只能經過前k個點的最短路
- 則轉移：$d[k+1][i][j] = min(d[k][i][j], d[k][i][k+1]+d[k][k+1][j])$
- 因此中間點k必須在最外層（不過[有論文指出](https://arxiv.org/pdf/1904.01210.pdf)順序顛倒一樣可以得到正確解）
- 優點：實作容易，缺點：時間 $O(v^3)$ 、無法處理負環（可處理負邊）

### Dijkstra’s：單點源最短路徑
- 優點：時間 $O(E+V^2)$、無法處理負邊
- 想法：Greedy（和DP）
- 維護：1. 未拜訪的節點集合$U$ 2. $d[i]$ 目前起點到 $i$ 最短路 3. 目前考慮節點 $p$
- 重複以下動作直到u為空：
    - 對於所有與 $p$ 連接的節點 $q$，$d[q] = min(d[q],d[p]+weight[p][q])$
    - 當 $p$ 相鄰節點都走過：在 $u$ 中移除 $p$
    - 將 $p$ 更新成U中離起點距離最短的點 $min(d[j])$
- 可以變成 $O((E+V)logV)$->邊較為稀疏的圖時有利（使用priority_queue）
- 不能處理負邊，因為 $d[i]$ 較小的處理完之後就不會再更動了，加入負邊可能更小
- 拿距離最小的點 $k$ 去更新其他點，不能保證更新後其他點一定是最短路
- 上一步走完 $k$ 連接所有邊後，從集合 $U$ 中移除，因為沒有負邊， $k$ 必定是最短路


### Bellman-Ford：單點源最短路徑
- 可以處理負環
- 時間：$O(VE)$
- 想法：Relax鬆弛
- 一條邊 $\delta(u,v)$ 滿足 $dis[v] = min(dis[v],dis[u]+weight[u][v])$
- 對每一條邊進行鬆弛，因為鬆弛沒有按照最短路順序，因此要做V-1次
- 此為暴力作法
- 執行V-1次的worst case：
    - 剛好跟最短路徑的順序相反
    - 每次 Relax 後只能優化單一子路徑
    - 共有V個頂點，需要有V-1 條子路徑，每一次一條
    - 檢查負環：做完之後卻有滿足$d[v] > d[u]+w(u,v)$ ，表示有負環
### 優化：SPFA(Shortest Path Faster Algorithm)
- 每次只relax更新過的點
- 使用queue優化，有點像BFS過程
    - 1.把起點 Push 到 Queue
    - 2.從 Queue 裡 Pop 出一筆資料
    - 3.該筆資料的所有邊進行 Relax
    - 4.有更新到的頂點再 Push 到 Queue
    - 5.重複步驟 2 ~ 4，直到 Queue 為空
- 時間：$O(VE)$ ->worst case，期望 $O(KE)$ ，K大概是2吧（反正挺快的） 

### DAG Shortest Path
首先對所有點進行拓墣排序，花上時間 $O(V+E)$，接著對每一條邊進行鬆弛，時間$O(E)$，因此總時間複雜度是 $O(V+E)$。這個時間複雜度是很快的，但相對的限制也非常多，除了不能有負邊與負環之外，更不能有正環在其中，否則不能進行拓墣排序（在之前筆記[進階圖論（一）](/OYm7TyO2RquZwdUFLx8PFQ)）有提到，也就是這一中圖必須是DAG(Directed Acyclic Graph)！

一個有趣的應用：[PERT](https://zh.wikipedia.org/wiki/%E8%A8%88%E7%95%AB%E8%A9%95%E6%A0%B8%E8%A1%93)

### 最短路徑樹
- 紀錄predecessor(樹父節點唯一)
- 起點到每個點的最短路徑都唯一的話，那把這些路徑疊起來會變成一棵樹
- 樹：每一點都有唯一來源（最短路）

### 最短路徑比較
最短路徑問題共有以下求解方式（當然還有一堆），整理比較圖：
![](https://i.imgur.com/uUUmhcL.jpg)

### 負環
上表中的可以處理負環的SPFA和Bellman-Ford是以什麼樣的方式處理？（遇到負環權重應該是$-\infty$）上方所謂負環是指下圖這種情況（當出發點為s，終點為t求最短路徑的問題），因為沒有經過負環，因此 $\delta(s,t)$ 可以被SPFA和Bellman-Ford求出正確的最短路徑為1。我們可以利用從終點回朔最短路徑（利用predecessor紀錄）看是否有重複經過的點，如果有則表示途中有經過負環！

![](https://i.imgur.com/MJnJXUp.png)

至於其他的算法，都會求出不正確的數值！

:::info

**Floyd warshall**
這個演算法是處理全點對的最短路徑，如果有負環，那一定有任兩點的最短距離是錯誤的。不過我們一樣可以利用Floyd-Warshall演算法判斷圖中是否有負環，只要<font color="#f00">檢查每一個點走到自己的距離是否為負</font> ，即$dis[i][i]<0$ 是否成立，如果成立表示圖中有負環。
<br>

**Dijkstra**
這個演算法要求的限制更多，圖中<font color="#f00">不可以有負邊</font>（更別提多個負邊組成的赴環），原因是在Dijkstra求最短路的過程中使用到貪心的想法，當我們從heap裡面取出目前距離最短的點之後，便不會再次被更新。如果有負邊的話，貪心法的過程會發生錯誤，導致得到不正確的答案。

![](https://i.imgur.com/Gkg2mex.jpg)

此圖中如果邊 $\delta(B,A)$ 為一負邊，當A被移出集合U中便不會有任何再次被更新的機會，但卻因為這條負邊的關係，導致從$s$到$A$的最短距離並不會被正確更新到！
:::
以上大概就是最短距離的演算法整理，還有一個全點對最短路徑Johnson’s Algorithm，大概就是對任一點做 Bellman-Ford（順便判斷有沒 有負環)，得到點權之後，用調整完的邊權做 V 次 Dijkstra，可以比Floyd-Warshall有更好的表現，到時候看。

## 上機作業
- [x] 可魚果運輸問題
- [x] 百慕達三角洲
- [x] 江神與他的小火車
- [x] 貨物運送計劃
- [x] E.漢米頓的麻煩
- [x] 旅遊規劃問題
- [x] Codeforces 543B: Destroying Roads

### 可魚果運輸問題
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

:::info
**複雜度分析**

以下分析一下演算法時間複雜度，總共有兩個時間複雜度，取決於使用何種資料結構來實作。

**1. 時間複雜度 $O(V^2+E)$**
首先是每一個點執行 $V$ 次對每一個相鄰的點進行更新，每一輪執行 $O(V)$ 尋找目前距離 $dis$ 最小的點。總共有 $E$ 條邊需要更新，因此最後要把 $E$ 再加上去。這樣總複雜度就會 $V\times O(V)+O(E) = O(V^2+E)$。此實作方式一般在稠密的圖中會有比較好的表現。
<br>

**2. 時間複雜度 $O((V+E)\log V)$**
這一題在 $O(n)$ 爆搜尋找改用heap去優化，因此複雜度就會是一樣執行V 輪利用 $O(logV)$ 找到距離 $dis$ 最小的點，同時將更新後的點放進heap中，我們知道放入heap的複雜度也是 $O(\log n)$，因此複雜度就會變成 $O(V\log V)+O(E\log V) = O((V+E)\log V)$。此實作方式一般在稀疏的圖中會有比較好的表現，從 $V$ 與 $E$ 的大小估計可以大致發現。
:::
以下是程式法的實作，最需要注意的地方是pair的first跟second儲存的東西。在存圖時會使用（點,權重）搭配vector；而在priority_queue中，因為要對權重進行比較，因此會用（權重,點），需要特別注意別搞混了!

:::success
**Priority_Queue實作細節**

預設的priority_queue會pop出目前heap中最大的元素（預設是less<>函數），如果要實作一個min heap 的話要使用greater<>函數，同時第二個傳進函數的參數要用一個vector！

```cpp=
priority_queue <Type, vector<Type>, ComparisonType > min_heap;
priority_queue <pii,vector<pii>,greater<pii>> pq;   
```
:::

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

### 百慕達三角洲
[題目連結](https://neoj.sprout.tw/problem/393/)
> 題目大意：
> 給定一張長n寬m的矩形圖，由"#"和"."組成，給定起點$(x_1,y_1)$以及終點$(x_2,y_2)$，必須最小化經過"."的次數，求最少需幾過幾次。

當下看到這一個題目的想法，就是跟處理一般的圖論題目一樣，將邊用vector儲存起來，將經過"."的權重設為很大的一個數字，這樣用Dijkstra做一次最短路徑就可以找出經過"."最小化的次數了！

以下是MLE的[Submission](https://neoj.sprout.tw/challenge/178136/)
![](https://i.imgur.com/9ZaisdZ.png)
吃了開心的MLE，如果當成一般的圖在處理，不管是在 $dis$ 開long long的處裡，或是開了一個vector陣列儲存邊，都非常的消耗空間。因此，我詢問了一下電神這一題的想法，他說我的想法用Dijkstra是正確的，不過在設定邊權的部分可以直接用0跟1代替，而且可以用queue去輔助實作BFS（要說它也可以是Dijkstra的另一種比較簡單的版本）。

![](https://i.imgur.com/9JldAha.png)

這題也就是所謂[0-1 BFS (Shortest Path in a Binary Weight Graph)](https://www.geeksforgeeks.org/0-1-bfs-shortest-path-binary-graph/)，或是[這裡0-1 BFS](https://codeforces.com/blog/entry/22276)，想法可以說是Dijkstra跟BFS的綜合（其實它跟SPFA也很相似）。以下是實作步驟：

1. 建立雙向的佇列(deque)，等等要存放被relax過的點，初始放入起點
2. 每一次從deque前方pop出一點，對那一點相鄰的所有點進行鬆弛
3. 如果被鬆弛時的邊權重為0，將點push dequeue的前方
4. 否則當鬆弛時的邊權重為1，將點push dequeue的後方
5. 重複執行2-4步驟直到deque為空

當我們一直利用deque最前端的元素進行鬆弛，因為我們將邊權為0的元素放入最前端，用距離最小的那些點進行鬆弛，每一個點最多會被鬆弛一次，因此總時間複雜度為$O(V+E)$，比起用Dijktra直接做$O((V+E)\log V)$快了許多（此演算法之所以正確是因為其中一邊的權重是0，不管0接到誰他的權重也都是0，有點像「從最小層逐漸擴展」的概念）！

:::warning
小問題（出處[這裡](https://codeforces.com/blog/entry/22276)）
1. Can we apply the same trick if our edge weights can only be 0 and x (x >= 0) ?
2. Can we apply the same trick if our edge weights are x and x+1 (x >= 0) ?
3. Can we apply the same trick if our edge weights are x and y (x,y >= 0) ?

:::spoiler 解答
YES,NO,NO
:::

這題之所以可行是因為有一邊的權重是0，當點皆以權重為0串再一起時，他會是最短的，使用最短去更新接下來的點，因此第一題是正確的！但第二題與第三題是錯誤的，考慮以下點與邊的情況：

![](https://i.imgur.com/BYojGwm.jpg)

當我依照01BFS的方法不斷去用x更新其他的點，更新完之後會發現點1到點3的最短路徑應該是x+1，到時候又要再重新Relax一次，複雜度會爆炸喔（比SPFA可能還慘，因為當點三利用兩個x更新完之後，用它來做跟3所有相鄰的點，做完卻發現$(1,3)$有更短的距離，又必須重新全部更新一次！）總結來說，他只是用於只有兩種邊的情況，且其中一邊必須為0。



![](https://i.imgur.com/wnXKbI8.png)

:::info
**比較一下記憶體用量**
最主要還是時間複雜度的比較，不過既然空間已經爆了，時間也沒法比了QQ

**MLE**

```cpp=
#define N 2005
#define N2 4000005
int n,m,q,predecessor[N2],dis[N2];
bool visit[N2],maze[N2];
vector<pii>edge[N2];
```
**AC**
```cpp=
#define N 2003
int n,m,dis[N][N];
bool visit[N][N],maze[N][N];
```
:::
以下是使用deque實作01BFS的AC code：

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define N 2003
#define FOR(i,n) for(int i=0;i<n;i++)
#define pii pair<int,int>
using namespace std;
int n,m,dis[N][N];
int dx[4] = {0,-1,0,1},dy[4] = {1,0,-1,0};
bool visit[N][N],maze[N][N];

void solve(){
    cin>>n>>m;
    memset(visit,0,sizeof(visit));
    for(int i=1;i<=n;i++){
        for(int j=1;j<=m;j++){
            char temp;cin>>temp;
            if(temp=='.')maze[i][j] = 1;
            else maze[i][j] = 0;
        }
    }
    deque<pii>deq;
    int s1,s2,e1,e2;cin>>s1>>s2>>e1>>e2;
    memset(dis,0x3f3f3f3f,sizeof(dis));
    dis[s1][s2] = 0;
    deq.push_back({s1,s2});
    while(!deq.empty()){
        int x = deq.front().first,y = deq.front().second;
        deq.pop_front();
        for(int i=0;i<4;i++){
            int nx = x+dx[i],ny = y+dy[i];
            if(nx<1||nx>n||ny<1||ny>m)continue;
            
            if(dis[nx][ny] > dis[x][y]+maze[nx][ny]){
                dis[nx][ny] = dis[x][y]+maze[nx][ny];
                if(maze[nx][ny]==0)deq.push_front({nx,ny});
                else deq.push_back({nx,ny});
            }
        }
    }
    cout<<dis[e1][e2]<<endl;
}

signed main(){
    Orz;
    int t;t = 1;
    while(t--){
        solve();
    }
}
```

### 江神與他的小火車
[題目連結](https://neoj.sprout.tw/problem/431/)

> 題目大意
> 已知有N個點以及M條邊，總共有Q筆詢問，每筆詢問有$\delta(a,b)$，表示新增一條由 $a$ 指向 $b$ 的邊（其權重為1），對每一筆詢問輸出從點 $1$ 到點 $N$ 的最短距離。

![](https://i.imgur.com/f96GUmd.jpg)

我們知道如果有用新加進來的邊 $\delta(a,b)$，則路徑可以被拆解成 $\delta(1,a)+\delta(a,b)+\delta(b,n)$。其中，我們可以用Dijkstra 一次尋找1到任何點的最短距離，那要如何處理路徑 $\delta(b,n)$ 呢？這就是**單一目的最短距離問題**，我們可以將所有的邊逆序，將終點做一次Dijkstra尋找最短路，這時候我們就可以用 $O(1)$ 的時間回答每一筆詢問，總時間複雜度：預處理$O((V+E)\log V)$，詢問 $O(Q)$。

:::warning
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
:::
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
### 貨物運送計劃
[題目連結](https://tioj.ck.tp.edu.tw/problems/1641)

> 題目敘述
> 給定N個點M條邊，第i條邊有方邊率$C_i$，假設目前運送貨物重量p經過此邊，代表經過邊i會需要多增加 $p\times C_i$ 的重量。給定起點、終點，求到達終點時最少的貨物重量為多少。

換種說法，題目要求的是每經過一條邊，就要乘上某一個數，要求到終點最小的重量。下圖是題目範例測資：
![](https://i.imgur.com/BSNWgh8.png)

$\delta(1,2)\to\delta(2,3)$，所付出的代價是$(1\times (1+1))\times (2+1)=6$。如果是$\delta(1,3)$ 的話直接$1\times (4+1)=5$，可以觀察到，遇到邊就需要將原本的數字乘上$C_i+1$。

我們可以透過將邊權取 $\log$ 之後，就可以利用Dijkstra進行最短路徑的計算，因為取 $\log$ 後的加減運算等同於原本的乘法運算，只要最後把算出來的答案次方即可！

這一題的輸出要求科學記號（為了要避免浮點數誤差），以下程式碼來達成（要求小數點後兩位，同時次方部分要求整數3位）：
```cpp=
printf("%.2fe+%03lld\n",pow(10,ans),x);
```
程式碼的部分，透過$edge$存完所有的取完 $\log$ 之後的邊，進行Dijkstra，輸出最短路徑（以科學記號表示）即可！

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define N 10005
#define FOR(i,n) for(int i=0;i<n;i++)
#define pii pair<int,int>
#define pid pair<int,double>
#define pdi pair<double,int>
using namespace std;
int n,m,s,t;
double dis[N];
bool visit[N];
vector<pid> edge[N];

void solve(){
    memset(visit,0,sizeof(visit));
    
    scanf("%lld %lld %lld %lld",&n,&m,&s,&t);
    for(int i=0;i<m;i++){
        int a,b;double c;scanf("%lld %lld %lf",&a,&b,&c);
        edge[a].push_back({b,(double)log10(c+1)});
    }
    fill(dis,dis+n+2,1e16);
    
    priority_queue<pdi,vector<pdi>,greater<pdi>> pq;
    dis[s] = 0.0;
    pq.push({0,s});
    
    while(!pq.empty()){
        int cur = pq.top().second;
        pq.pop();
        if(visit[cur])continue;
        visit[cur] = 1;
        
        for(auto i:edge[cur]){
            int next = i.first;
            double w = i.second;
            if(dis[next] > dis[cur]+w){
                dis[next] = dis[cur]+w;
                pq.push({dis[next],next});
            }
        }
    }
    double ans = dis[t];
    int x = floor(ans);
    ans-=x;
    
    printf("%.2fe+%03lld\n",pow(10,ans),x);
}

signed main(){
    int t;t = 1;
    while(t--){
        solve();
    }
}
```

### E.漢米頓的麻煩
[題目連結](https://tioj.ck.tp.edu.tw/problems/1096)

他題目時不時提到漢米頓，是多愛漢米頓XDD（這一題跟漢米頓根本沒關係）

> 題目大意
> 在一張n(n≤100)個點的圖中，尋找路徑長度最短的環

那就用**Floyd-Warshall**找最小的環就好！

:::info
**Floyd-Warshall**
這就是dp的作法，用 $O(N^3)$ 的時間進行轉移，就能得到全點對的最短路徑。這邊有一個重要的轉移順序，也就是中點-起點-終點進行轉移，如果把dp展開就會發現中點必須在最外層進行轉移：

> 定義 $dp[k][i][j]$ 為點 $i$ 走到點 $j$ ，只能經過前k個點的最短路徑，則轉移式變成：
> $$d[k+1][i][j] = min(d[k][i][j], d[k][i][k+1]+d[k][k+1][j])$$

每一個k+1都是由k轉移而來，仰賴k的點的配對，因此必須最先轉移中點k的部分！（不過順序顛倒也不會怎樣啦，上面「課程內容」的地方有寫到）
:::
同樣這一份code也可以判斷有沒有負環，只要ans小於0的話代表有負環（有的話距離是不能用的喔，因為Floyd-Warshall不能處理負環，但負邊是可以處理的）

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define N 101
#define FOR(i,n) for(int i=0;i<n;i++)
#define pii pair<int,int>
#define pid pair<int,double>
#define pdi pair<double,int>
using namespace std;
int n,dp[N][N];

void solve(){
    memset(dp,0x3f3f3f3f,sizeof(dp));
    for(int i=0;i<n;i++){
        for(int j=0;j<n;j++){
            int temp;cin>>temp;
            if(temp!=0)dp[i][j] = temp;
        }
    }
    for(int k=0;k<n;k++){
        for(int i=0;i<n;i++){
            for(int j=0;j<n;j++){
                dp[i][j] = min(dp[i][j],dp[i][k]+dp[k][j]);
            }
        }
    }
    int ans = INT_MAX;
    for(int i=0;i<n;i++){
        ans = min(ans,dp[i][i]);
    }
    if(ans == INT_MAX)cout<<-1<<endl;
    else cout<<ans<<endl;
}

signed main(){
    while(cin>>n && n!=0){
        solve();
    }
}
```

### 旅遊規劃問題
[題目連結](https://tioj.ck.tp.edu.tw/problems/1028)
[Submission](https://tioj.ck.tp.edu.tw/submissions/262198)
這一題 $n≤13$ 大概就是位元dp來完成（狀態壓縮），定義 $dp[i][j]$ 為現在在點i上，拜訪過點集j的最短距離（j中不包含點i）。這裡使用到了同層轉移的技巧，也就是利用j中的點，對相同狀態下不在i中的點進行更新。

點集j中在狀態i下為起點，用Dijkstra對不在i中的其他點進行更新，最後的答案即為$dp[s][S-(1<<s)]$。

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define double long double
#define N 14
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define pii pair<int,int>
using namespace std;
int n,m;
vector<pii> edge[N];
int dis[N],dp[N][1<<N],pre[N][1<<N];
bool visit[N];
//定義dp[i][j]為現在在點i，拜訪過點集合j（i不在點集j中）

signed main(){
    cin>>n>>m;
    rep(i,1,m){                                 //讀入m條邊
        int a,b,w;cin>>a>>b>>w;
        edge[a].push_back({b,w});
        edge[b].push_back({a,w});
    }
    int S = 0,start = -1;cin>>m;                //S為待拜訪的所有點集合
    rep(i,1,m){
        int temp;cin>>temp;
        S = S|(1<<temp);
        if(start == -1)start = temp;
    }
    
    rep(i,0,n)rep(j,0,(1<<n))dp[i][j] = INF;
    rep(i,0,n)dp[i][0] = 0;
    
    for(int i=1;i<(1<<n);i++){
        if(i == (S & i)){                       //在集合i中的點在S一定要有
            priority_queue<pii,vector<pii>,greater<pii>> pq;
            fill(dis,dis+n,INF);                //將dis初始為無限大，做Dijkstra
            memset(visit,0,sizeof(visit));
            for(int j=0;j<n;j++){
                if(i&(1<<j)){                   //從有在i中的點向不在的點做Dijkstra
                    dis[j] = dp[j][i^(1<<j)];   //目前在j，經過拜訪了點集i（不包含點j）
                    pq.push({dis[j],j});        //利用在i中的點對其他點進行更新
                }
            }
            while(!pq.empty()){
                int cur = pq.top().second;
                pq.pop();
                visit[cur] = 1;
                for(auto k : edge[cur]){
                    int v = k.first,w = k.second;
                    if(i&(1<<v))continue;
                    if(dis[v] > dis[cur]+w){
                        dis[v] = dis[cur] + w;
                        pre[v][i] = cur;        //紀錄點v在狀態i下由cur轉移
                        pq.push({dis[v],v});
                    }
                    else if(dis[v] == dis[cur]+w && pre[v][i] > cur){
                        pre[v][i] = cur;
                    }
                }
            }
            for(int j=0;j<n;j++){               //對於不在點集i中的點進行更新
                if(i & (1 << j))continue;
                dp[j][i] = dis[j];              //同層狀態轉移，更新dp[j][i]
            }
        }
    }
    cout<<"Minimum travel distance: "<<dp[start][S^(start)]<<endl;
    cout<<"Travel route:";
    int cur = start;S = S^(1<<start);
    while(true){
        cout<<" "<<cur;
        if(!S)break;
        cur = pre[cur][S];
        if(S&(1<<cur))S = (S^(1<<cur));         //經過的點不一定是指定的觀光點
    }
    cout<<endl;
}
```

### Codeforces 543B: Destroying Roads
[題目連結](https://codeforces.com/problemset/problem/543/B)
[Submission](https://codeforces.com/problemset/submission/543/125743532)

> 題目大意
> 給定N個點M條無向邊，每一邊權重都是1，以及兩筆資料，由三個數$(x,y,w)$ 構成，代表起點為x、終點為x、要在w距離內從起點走到終點。試問最多可以拔掉幾條邊同時滿足以上兩個資料所描述的特性？

這一題蠻有趣的，首先他的邊權重都是1，因此我們可以直接用BFS尋找最短路徑，並且點第一次拜訪到時的就是該點的最短距離（BFS一層一層擴展）。

:::info
**第一個想法：錯誤想法**
我先將兩個起點與終點的最短路徑都找出來，把將過的邊都標上不能移除，將其他的邊全部拔掉。

**問題點：**
固然找到的是最短路徑，但不代表可以最大化拔除邊的數量。因為題目要求兩點只要符合最短距離 $w$ 即可，因此每一個配對其實不用符合是最短路徑的情況（如下圖）！目標是在題目要求的限度內最大化重複的邊，使能夠被拔除的邊最大化！

![](https://i.imgur.com/QfbfF82.png)

上圖紅色線段是$\delta(1,7)$的最短路徑、褐色是線段$\delta(3,6)$的最短路徑、黑色線段是皆以最短路徑之下可以被拔除的邊。但是如果將路徑$\delta(3,6)$換成是$(3,2)\to(2,4)\to(4,5)\to(5,6)$，被拔除的邊可以增過為三條。
:::

這一題最重要的關鍵就是以$O(V^2)$枚舉所有點對（距離長度可以直接換算成邊的數量），可以先用$O(V(V+E)$的時間對每一個點用進行BFS，如此一來總複雜度即為$O(V^2+VE)$，所幸題目給定總共的邊數不會超過3000條，因此是可以在時間限制內完成枚舉。

:::warning
枚舉路徑 $\delta(i,j)$ 為共同邊的時候，必須考慮起點與終點的方向，同時考慮從 $i\to j$ 以及從 $j\to i$ 兩個方向。以下圖為例，當枚舉都使用相同的起點以及終點，會讓下圖的 $(3,4)$ 被重複計算！

![](https://i.imgur.com/tjq8oe7.png)
:::

以下是AC程式碼：
```cpp=
#include <bits/stdc++.h>
#define ll long long
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define N 3005
#define FOR(i,n) for(int i=0;i<n;i++)
#define pii pair<int,int>
#define pid pair<int,double>
#define pdi pair<double,int>
using namespace std;
int n,m,s1,e1,w1,s2,e2,w2,dis[N][N];
bool visit[N];
vector<int> edge[N];

int main(){
    cin>>n>>m;
    for(int i=0;i<m;i++){
        int a,b;cin>>a>>b;
        edge[a].push_back(b);
        edge[b].push_back(a);
    }
    cin>>s1>>e1>>w1>>s2>>e2>>w2;
    memset(dis,0x3f3f3f3f,sizeof(dis));
    
    for(int i=1;i<=n;i++){
        memset(visit,0,sizeof(visit));
        queue<int>que;
        que.push(i);
        visit[i] = 1;
        dis[i][i] = 0;
        while(!que.empty()){
            int cur = que.front();
            que.pop();
            for(auto j:edge[cur]){
                if(visit[j])continue;
                dis[i][j] = dis[i][cur]+1;
                visit[j] = 1;
                que.push(j);
            }
        }
    }
    if(dis[s1][e1]>w1||dis[s2][e2]>w2){
        cout<<-1<<endl;
        return 0;
    }
    int ans = dis[s1][e1]+dis[s2][e2];

    //O(n^2)枚舉所有共同邊
    for(int i=1;i<=n;i++){
        for(int j=1;j<=n;j++){
            if(dis[s1][i]+dis[i][j]+dis[j][e1]<=w1&&dis[s2][i]+dis[i][j]+dis[j][e2]<=w2)
                ans = min(ans,dis[s1][i]+dis[i][j]+dis[j][e1]+dis[s2][i]+dis[j][e2]);
            if(dis[s1][i]+dis[i][j]+dis[j][e1]<=w1&&dis[e2][i]+dis[i][j]+dis[j][s2]<=w2)
                ans=min(ans,dis[s1][i]+dis[i][j]+dis[j][e1]+dis[e2][i]+dis[j][s2]);
            
        }
    }
    cout<<m-ans<<endl;
}
```

###  TIOJ 2180 勇者冒險 (Adventure)
[題目連結](https://tioj.ck.tp.edu.tw/problems/2180)
[Submission](https://tioj.ck.tp.edu.tw/submissions/262669)

> 題目敘述
> 給定一個地圖共 $R\times C$ 行，接著會有 $M$ 行每一行都有一個座標和等級，代表經過這個座標需要多少等級才能通過。問從特定起點到終點所需要的最低的等級為多少。

這題蠻特別的，他用到了Dijkstra中每一次都找最小的去更新，同時因為要求的是路徑上最大的等級為何，因此也用到了類似BFS的手法去擴展每一個座標。

因為題目保證至少會有一條路徑通往起點與終點，同時我們每一次從queue拿出來的都是目前等級最低的點，因此如果碰到終點就可以確定這一條路線一定是需要等級最小的一條路徑！

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define double long double
#define N 1005
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define rrep(i,l,r) for(int i=l;i<r;i++)
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int n,row,col,mp[N][N],dis[N][N];
int s1,s2,e1,e2;
int dx[4] = {0,-1,0,1},dy[4] = {1,0,-1,0};
bool visit[N][N];
typedef pair<int ,pair<int,int>> pp;

void Dijkstra(){
    //Dijkstra
    priority_queue<pp,vector<pp>,greater<pp>> pq;
    dis[s1][s2] = 0;
    pq.push({0,{s1,s2}});
    
    while(!pq.empty()){
        int rr = pq.top().y.x,cc = pq.top().y.y;
//        cout<<rr<<" "<<cc<<"  "<<dis[rr][cc]<<endl;
        pq.pop();
        for(int i=0;i<4;i++){
            int nx = rr+dx[i],ny = cc+dy[i];
            if(nx<0||ny<0||nx>=row||ny>=col)continue;
            if(nx == e1 && ny == e2){
                cout<<dis[rr][cc]<<endl;
                return;
            }
            if(mp[nx][ny] == -1)continue;
            if(visit[nx][ny])continue;
            dis[nx][ny] = max(dis[rr][cc],mp[nx][ny]);
            pq.push({dis[nx][ny],{nx,ny}});
            visit[nx][ny] = 1;
        }
    }
}

signed main(){
    Orz;
    cin>>row>>col;
    memset(dis,0,sizeof(dis));
    memset(visit,0,sizeof(visit));
    memset(mp,-1,sizeof(mp));
    cin>>s1>>s2>>e1>>e2;
    cin>>n;
    while(n--){
        int a,b,c;cin>>a>>b>>c;
        mp[a][b] = c;
    }
    Dijkstra();
}
```

### 最小花費的航空之旅
> 題目敘述：
> 給定很多種連接城市間的聯票，要求從起始站搭乘，可以在中途任意站下車，但下車以後就不能再次上車。輸入的第一行為一個正整數 n (1 ≤ n ≤ 20)，即聯票的種類數。以下 n 行每航為一個聯票的資訊，其中第一個整數為聯票的價格，然後是聯票上城市的數目。
> 
> 接下來為一個行程單的資訊，其中第一個正整數為行程單上的城市數目k（包括起始城市，2 ≤ k ≤ 10），以及這些城市的編號（按順序列出）。
> 
> 輸出最小花費和對應的方案的其中一組解。

這一題是APCS Class的其中一題，難度頗高，因為他要找最短的路徑，同時增加了可以在任意站下車的條件。首先我們要維護每一個聯票的資訊，它的價值、起點、編號以及經過的城市。接著，我們考慮所有從起點出發的聯票，將每一種聯票上的每一個經過的城市都加入queue中。

跟上一題一樣，當我們每一次都從cost最小的點下手，在轉移的過程中如果將所有的點都走訪一遍，則他的路徑必定是最短的，因為每一次的更新都是從花費最小的路徑去轉移。

:::info
**使用自定義比較函數放入Priority_queue中**

Priority_queue中的比較函數需要使用到struct或class包覆的比較函數，若要回傳cost的最小值，則必須以大於來寫（有點像預設是pop出最大值，使用的卻是less<int>的比較函數）

```cpp=
struct cmp{
    bool operator()(node a,node b){
        return a.cost > b.cost;
    }
};
```
:::

以下是AC Code：
```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define double long double
#define N 105
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define rrep(i,l,r) for(int i=l;i<r;i++)
#define pii pair<int,int>
#define pif pair<int,float>
#define x first
#define y second
using namespace std;
int n,k,visit[20];

struct Ticket{
    int id,cost;
    vector<int> next_city;
    //陣列ticket[i]存以i為起點
};
vector<Ticket> ticket[30];          //ticket[i] 起點為i的聯票

struct node{
    int cost,cur_pos,visit_pos;
    vector<int> used_ticket;
    //cur_pos現在所在位置、visit_pos在行程單上位置
};

struct cmp{
    bool operator()(node a,node b){
        return a.cost > b.cost;
    }
    //讓priority_queue可以pop出最小的cost的路徑
};
priority_queue<node,vector<node>,cmp> pq;

signed main(){
    Orz;
    cin>>n;
    rep(i,1,n){
        int cost,num,s;cin>>cost>>num>>s;
        vector<int> temp;
        rep(j,1,num-1){
            int k;cin>>k;
            temp.push_back(k);
        }
        ticket[s].push_back({i,cost,temp});
    }
    cin>>k;
    rep(i,1,k)cin>>visit[i];
    
    for(auto i : ticket[visit[1]]){         //行程第一個起點的聯票起點
        int p = 1;
        for(auto j : i.next_city){
            if(p < k && j == visit[p+1])p++;
            pq.push({i.cost,j,p,{i.id}});
//            cout<<"一開始聯票："<<i.id<<" "<<j<<endl;
        }
    }
    while(!pq.empty()){
        node cur = pq.top();
        pq.pop();
        if(cur.visit_pos == k){
            cout<<"Cost = "<<cur.cost<<", Tickets used: "
                <<cur.used_ticket[0];
            for(int i=1;i<cur.used_ticket.size();i++)
                cout<<", "<<cur.used_ticket[i];
            cout<<endl;
            break;
        }
        for(auto i : ticket[cur.cur_pos]){
            vector<int> vec(cur.used_ticket);
            vec.push_back(i.id);                //將現在使用的第i聯票推入
            int p = cur.visit_pos;
            for(auto j : i.next_city){
                if(p < k && j == visit[p+1])p++;
                pq.push({cur.cost+i.cost,j,p,vec});
            }
        }
    }
}
```