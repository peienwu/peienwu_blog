---
title: "[題解]NEOJ 393 百慕達三角洲"
date: 2021-1-7
tags: 
    - 最短路徑
    - 題解
categories: 最短路徑題解
mathjax: true
---

### 百慕達三角洲
<!-- more -->
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

{% note success %}
小問題（出處[這裡](https://codeforces.com/blog/entry/22276)）

1. Can we apply the same trick if our edge weights can only be 0 and x (x >= 0) ?
2. Can we apply the same trick if our edge weights are x and x+1 (x >= 0) ?
3. Can we apply the same trick if our edge weights are x and y (x,y >= 0) ?

解答YES,NO,NO
{% endnote %}

這題之所以可行是因為有一邊的權重是0，當點皆以權重為0串再一起時，他會是最短的，使用最短去更新接下來的點，因此第一題是正確的！但第二題與第三題是錯誤的，考慮以下點與邊的情況：

![](https://i.imgur.com/BYojGwm.jpg)

當我依照01BFS的方法不斷去用x更新其他的點，更新完之後會發現點1到點3的最短路徑應該是x+1，到時候又要再重新Relax一次，複雜度會爆炸喔（比SPFA可能還慘，因為當點三利用兩個x更新完之後，用它來做跟3所有相鄰的點，做完卻發現$(1,3)$有更短的距離，又必須重新全部更新一次！）總結來說，他只是用於只有兩種邊的情況，且其中一邊必須為0。

![](https://i.imgur.com/wnXKbI8.png)

{% note info %}
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

{% endnote %}
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
