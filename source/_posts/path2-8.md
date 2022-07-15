---
title: "[題解]TIOJ 2180 勇者冒險(Adventure)"
date: 2021-1-7
tags: 
    - 最短路徑
    - 題解
categories: 最短路徑題解
mathjax: true
---


### TIOJ 2180 勇者冒險 (Adventure)
<!-- more -->
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
