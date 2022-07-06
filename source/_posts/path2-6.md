---
title: "[題解]TIOJ 1028 旅遊規劃問題"
date: 2021-1-7
tags: 
    - 最短路徑
    - 題解
categories: 最短路徑題解
mathjax: true
---

### 旅遊規劃問題
<!-- more -->
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
