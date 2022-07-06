---
title: "[題解]Codeforces 543B: Destroying Roads"
date: 2021-1-7
tags: 
    - 最短路徑
    - 題解
categories: 最短路徑題解
mathjax: true
---

### Codeforces 543B: Destroying Roads
<!-- more -->
[題目連結](https://codeforces.com/problemset/problem/543/B)
[Submission](https://codeforces.com/problemset/submission/543/125743532)

> 題目大意
> 給定N個點M條無向邊，每一邊權重都是1，以及兩筆資料，由三個數$(x,y,w)$ 構成，代表起點為x、終點為x、要在w距離內從起點走到終點。試問最多可以拔掉幾條邊同時滿足以上兩個資料所描述的特性？

這一題蠻有趣的，首先他的邊權重都是1，因此我們可以直接用BFS尋找最短路徑，並且點第一次拜訪到時的就是該點的最短距離（BFS一層一層擴展）。

{% note primary %}
**第一個想法：錯誤想法**
我先將兩個起點與終點的最短路徑都找出來，把將過的邊都標上不能移除，將其他的邊全部拔掉。

**問題點：**
固然找到的是最短路徑，但不代表可以最大化拔除邊的數量。因為題目要求兩點只要符合最短距離 $w$ 即可，因此每一個配對其實不用符合是最短路徑的情況（如下圖）！目標是在題目要求的限度內最大化重複的邊，使能夠被拔除的邊最大化！

![](https://i.imgur.com/QfbfF82.png)

上圖紅色線段是$\delta(1,7)$的最短路徑、褐色是線段$\delta(3,6)$的最短路徑、黑色線段是皆以最短路徑之下可以被拔除的邊。但是如果將路徑$\delta(3,6)$換成是$(3,2)\to(2,4)\to(4,5)\to(5,6)$，被拔除的邊可以增過為三條。
{% endnote %}

這一題最重要的關鍵就是以$O(V^2)$枚舉所有點對（距離長度可以直接換算成邊的數量），可以先用$O(V(V+E)$的時間對每一個點用進行BFS，如此一來總複雜度即為$O(V^2+VE)$，所幸題目給定總共的邊數不會超過3000條，因此是可以在時間限制內完成枚舉。

{% note primary %}
枚舉路徑 $\delta(i,j)$ 為共同邊的時候，必須考慮起點與終點的方向，同時考慮從 $i\to j$ 以及從 $j\to i$ 兩個方向。以下圖為例，當枚舉都使用相同的起點以及終點，會讓下圖的 $(3,4)$ 被重複計算！

![](https://i.imgur.com/tjq8oe7.png)
{% endnote %}

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

### TIOJ 2180 勇者冒險 (Adventure)

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

{% note primary %}
**使用自定義比較函數放入Priority_queue中**

Priority_queue中的比較函數需要使用到struct或class包覆的比較函數，若要回傳cost的最小值，則必須以大於來寫（有點像預設是pop出最大值，使用的卻是less<int>的比較函數）

```cpp=
struct cmp{
    bool operator()(node a,node b){
        return a.cost > b.cost;
    }
};
```

{% endnote %}

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
