---
title: "[題解]TIOJ 1641 貨物運送計劃"
date: 2021-1-7
tags: 
    - 最短路徑
    - 題解
categories: 最短路徑題解
mathjax: true
---

### 貨物運送計劃
<!-- more -->
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
