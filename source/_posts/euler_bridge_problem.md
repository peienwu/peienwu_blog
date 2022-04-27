---
title: 七橋問題、最短路徑問題及其延伸
date: 2022-1-11
tags: 
    - 數學
    - 離散數學
    - 多元選修
categories:
	- 數學筆記

mathjax: true
---

這篇文章的主題有以下：

1. 歐拉路徑、迴路（七橋問題）
2. 漢米頓路徑、迴路
3. 最短路徑演算法（Dijkstra,Bellman-Ford,Floyd-Warshall）
4. 旅行業務員問題（TSP）

<!--more-->

## 歐拉路徑、迴路（七橋問題）

### 問題簡介

![](https://i.imgur.com/buHMNgV.png)

七橋問題的描述是：該如何在所有橋都只能走一遍的前提下，把這個地方所有的橋都走遍（每條邊洽經過一次）？

由數學歸納法得證，我們只要統計每個點的度數並記錄奇點的個數，只要大於二則不存在歐拉路徑。

### 實作程序

**1. 判斷奇點個數**，若奇點個數k：

* k > 2，那麼無解
* k = 2，則選擇其中一個奇點作為起點
* k = 0，則選擇任意一個點作為起點

**2. DFS 執行下列步驟**
若當前節點還有尚未走過的邊，那麼拜訪該邊，並在拜訪完後輸出該邊，否則離開當前結點

**3. 若還有節點尚未拜訪，則無解**
**4. 否則輸出順序即為一組解**

### 程式碼實作

```cpp=
#include <bits/stdc++.h>
#define N 501
using namespace std;
int n,Edge[N][N],ans[1025],ind = 0;

void DFS(int cur){
    for(int i=1;i<=500;i++){
        if(Edge[cur][i]){
            Edge[cur][i]--;Edge[i][cur]--;
            DFS(i);
        }
    }
    ans[ind++] = cur;
}

signed main(){
    cin>>n;
    memset(Edge, 0, sizeof(Edge));
    for(int i=0;i<n;i++){
        int a,b;cin>>a>>b;
        Edge[a][b]++;
        Edge[b][a]++;
    }
    int start = 1;                //開始的節點編號
    for(int i=1;i<=500;i++){
        int sum = 0;
        for(int j=1;j<=500;j++){
            sum+=Edge[i][j];
        }
        if(sum%2!=0){             //找到第一個度數為奇數的節點
            start = i;
            break;
        }
    }
    DFS(start);
    for(int i=ind-1;i>=0;i--)cout<<ans[i]<<endl;
}
```

## 旅行業務員問題（TSP）與漢米頓路徑

### 問題描述

![](https://i.imgur.com/Zb3ihAn.png)

漢米頓路徑是類似於歐拉路徑，但每一條邊恰好經過一次的限制變成點恰好經過一次，但此問題的難度比歐拉迴路高上好幾倍，為一個NP-Complete的問題。

![](https://i.imgur.com/W5izpYM.png)

旅行商務員問題則是描述平面上n個城市，是否存在一條路徑將每一個點恰好走過一次後回到出發點。此問題為一個漢米頓迴路問題的經典問題。

[題目連結](https://neoj.sprout.tw/problem/187/)
第一次寫**TSP**(Traveling Salesman Problem)，題目敘述如下：
> 給定一系列城市和每對城市之間的距離，求解存取每一座城市一次並回到起始城市的最短迴路。

### 實作程序

我們透過位元的動態規劃，可以將原本$o(n!)$的時間複雜度變成以$O(n^2\cdot 2^n)$的複雜度解決這個問題。

這是一個已經被證明$NP-Hard$ 的問題，暴力作法是要檢查所有路徑的情況，因為共有n個點，每一個點又連接n-1個點，繼續下去總共會有N!種情況，因此複雜度為$O(n!)$。
總共的點數共有n個，根據多邊形的邊數公式可以知道一共有$\frac{n^2-n}{2}$條邊，每一條邊都有各自的距離。
如果改用DP做，時間複雜度可以壓到時間複雜度$O(n^2\cdot2^n)$，比$O(n!)$還優秀！這一題用到的是DP優化中的<font color="#f00">狀態壓縮</font>，具體的實作細節如下：

{% note success %}
**位元運算**
在這一題需要用到位元運算，用16個bit表示每一個點有沒有被走訪過。用這樣的表示方法可以讓code更為節儉，也就是狀態壓縮的概念。

**左移運算子(<<)**
這一題會一直反覆被用到，1<<t代表把1往左移動t單位，用10進位表示就是$2^t$。每往左移動一格，數字就會變成原來的兩倍！

**狀態壓縮**
將十進位整數s以二進位表示，會得到一串01字串，假設s=10，則 $s = 1010_{(2)}$。以這題來說，這樣的字串我們可以用來表示第4和第2個點已經被拜訪，而第3跟第1個點還沒有被拜訪。
{% endnote %}

#### 定義

定義 $dp[n][s]$ 表示目前在第n點上，s為走過的點（狀態壓縮）的最短距離

#### 轉移式

$dp[n][s] = min(dp[i][s-(1<<n)]+dis[i][n])$, for all i such that s&(1<<i)!=0

這個轉移式很有意思，因為s代表了每一個點是否有被走過，當我要更新dp[n][s]時，我要確保此時的狀態s中的點n必須為1。同時，因為1<<i用二進位表示只有第i位會是1其他都是0，做and運算就看s的第i位決定結果。

對於每一個i必須確保第i點在狀態s中有被造訪，因此有了後面的條件。另外轉移式中的s-(1<<N)是把第n個點從未造訪的狀態轉移。

#### 邊界

$dp[0][1] = 0,\quad dp[i][j]= \infty$

距離的預設狀態為無限大，第一個點的初始狀態是最短距離0

#### 實作小細節

**迴圈順序**
在轉移的過程中，迴圈的第一層必須是狀態，第二層才是城市。如果顛倒過來的話，會導致前面的城市在狀態還沒有被更新的時候就已經失去了之後被更新的機會，因此城市的迴圈必須放在第二層！

**求答案**
因為我們要求的是回到原點的最短距離，因此在全部轉移完成之後，利用一個迴圈把回去原點的路的距離加上去，求得最小值。

### 程式碼

```cpp=
#include <bits/stdc++.h>
#define N 16
#define mod 1000000007
#define FOR(i,n) for(int i=0;i<n;i++)
#define ios ios::sync_with_stdio(0)
using namespace std;
int t;

void solve(){
    int n,dis[20][20];cin>>n;
    memset(dis,0,sizeof(dis));
    
    for(int i=0;i<n-1;i++){
        for(int j=i+1;j<n;j++){
            cin>>dis[i][j];
            dis[j][i] = dis[i][j];
        }
    }
    
    int dp[N][66000],m = 1<<n;
    memset(dp,0x3f3f3f3f,sizeof(dp));
    dp[0][1] = 0;   //將0點到自己的距離設為1
    
    //O(n^2 2^n)
    for(int i=0;i<m;i++){            //i表示2^n每一種狀態
        for(int j=0;j<n;j++){        //j表示城市
            if(!(i&(1<<j)))continue; //j城市要是被造訪過的狀態
            for(int k=0;k<n;k++){    //可以到的所有點中的狀態
                if(i & 1<<k){        //確保轉移過去的有被造訪過
                    dp[j][i] = min(dp[j][i],dp[k][i-(1<<j)]+dis[j][k]);
                }
            }
        }
    }
    int ans = INT_MAX;
    for(int i=0;i<n;i++)ans = min(ans,dp[i][m-1]+dis[i][0]);
    cout<<ans<<endl;
}

int main(){
    ios;
    cin>>t;
    while(t--){
        solve();
    }
}
```

## 最短路徑演算法

### Floyd-Warshall：全點對最短路徑(All Pairs)

* 不支援負環

* 想法：DP轉移（三個迴圈中點、起點、終點依序鬆弛）
* $d[i][j] = mid(d[i][j],d[i][k]+d[I][k]+d[k][j])$
* 如果改寫成定義 $dp[k][i][j]$ 為點 $i$ 走到點 $j$ ，只能經過前k個點的最短路
* 則轉移：$d[k+1][i][j] = min(d[k][i][j], d[k][i][k+1]+d[k][k+1][j])$
* 因此中間點k必須在最外層（不過[有論文指出](https://arxiv.org/pdf/1904.01210.pdf)順序顛倒一樣可以得到正確解）
* 優點：實作容易，缺點：時間 $O(v^3)$ 、無法處理負環（可處理負邊）

### Dijkstra’s：單點源最短路徑

* 優點：時間 $O(E+V^2)$、無法處理負邊

* 想法：Greedy（和DP）
* 維護：1. 未拜訪的節點集合$U$ 2. $d[i]$ 目前起點到 $i$ 最短路 3. 目前考慮節點 $p$
* 重複以下動作直到u為空：
  * 對於所有與 $p$ 連接的節點 $q$，$d[q] = min(d[q],d[p]+weight[p][q])$
  * 當 $p$ 相鄰節點都走過：在 $u$ 中移除 $p$
  * 將 $p$ 更新成U中離起點距離最短的點 $min(d[j])$
* 可以變成 $O((E+V)logV)$->邊較為稀疏的圖時有利（使用priority_queue）
* 不能處理負邊，因為 $d[i]$ 較小的處理完之後就不會再更動了，加入負邊可能更小
* 拿距離最小的點 $k$ 去更新其他點，不能保證更新後其他點一定是最短路
* 上一步走完 $k$ 連接所有邊後，從集合 $U$ 中移除，因為沒有負邊， $k$ 必定是最短路

```
DIJKSTRA(G, w, s)
    INITIALIZE-SINGLE-SOURCE(G, s)
      S <- Ø
      Q <- V[G]
      while Q ≠ Ø
          do u <- EXTRACT-MIN(Q)
              S <- S ∪ {u}
              for each vertex v ∈ Adj[u]
                  do RELAX(u,v,w)
```

### Bellman-Ford：單點源最短路徑

* 可以處理負環

* 時間：$O(VE)$
* 想法：Relax鬆弛
* 一條邊 $\delta(u,v)$ 滿足 $dis[v] = min(dis[v],dis[u]+weight[u][v])$
* 對每一條邊進行鬆弛，因為鬆弛沒有按照最短路順序，因此要做V-1次
* 此為暴力作法
* 執行V-1次的worst case：
  * 剛好跟最短路徑的順序相反
  * 每次 Relax 後只能優化單一子路徑
  * 共有V個頂點，需要有V-1 條子路徑，每一次一條
  * 檢查負環：做完之後卻有滿足$d[v] > d[u]+w(u,v)$ ，表示有負環

```
BELLMAN-FORD(G,w,s)
      INITIALIZE-SINGLE-SOURCE(G,s)
      for i <- 1 to |V[G]|-1
          do for each edge (u,v)∈ E[G]
              do RELAX(u,v,w)
      for each edge (u,v)∈ E[G]
          do if d[v] > d[u]+w(u,v)
              then return FALSE
      return TRUE
```

### 優化：SPFA(Shortest Path Faster Algorithm)

* 每次只relax更新過的點

* 使用queue優化，有點像BFS過程
  * 1.把起點 Push 到 Queue
  * 2.從 Queue 裡 Pop 出一筆資料
  * 3.該筆資料的所有邊進行 Relax
  * 4.有更新到的頂點再 Push 到 Queue
  * 5.重複步驟 2 ~ 4，直到 Queue 為空
* 時間：$O(VE)$ ->worst case，期望 $O(KE)$ ，K大概是2吧（反正挺快的）

```
Shortest-Path-Faster-Algorithm(G, s)
      for each vertex v ≠ s in V(G)
          d(v) := ∞
      d(s) := 0
      offer s into Q
      while Q is not empty
          u := poll Q
          for each edge (u, v) in E(G)
              if d(u) + w(u, v) < d(v) then
                  d(v) := d(u) + w(u, v)
                 if v is not in Q then
                     offer v into Q
```

### DAG Shortest Path

首先對所有點進行拓墣排序，花上時間 $O(V+E)$，接著對每一條邊進行鬆弛，時間$O(E)$，因此總時間複雜度是 $O(V+E)$。這個時間複雜度是很快的，但相對的限制也非常多，除了不能有負邊與負環之外，更不能有正環在其中，否則不能進行拓墣排序（在之前筆記進階圖論（一）有提到，也就是這一中圖必須是DAG(Directed Acyclic Graph)！

一個有趣的應用：[PERT](https://zh.wikipedia.org/wiki/%E8%A8%88%E7%95%AB%E8%A9%95%E6%A0%B8%E8%A1%93)

## 延伸筆記

* 最短路徑問題
* 進階圖論（一）
* 最短路徑例題
