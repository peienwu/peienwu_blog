---
title: "[題解]NEOJ 44 喵喵抓老鼠"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 喵喵抓老鼠
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/44/)
這一題是用BFS找最短路徑，因為沒有權重，所以可以直接用BFS看多久會最先走到老鼠
x y 座標原本用2個queue 分別存x跟y ，沒想到MLE了，後來改用struct，還是MLE!!
不過在27行的地方有一個Bug，害我找了好久...去問了<font color="#f00">電神</font>，才發現 visit 應該要在push 的時候就紀錄了，不要等到pop的時候，不然很多重複的會被push 進去（簡單來說，一個位置只能被push一次）

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define MAX 102
using namespace std;
int n,sx,sy;
int dx[4]={-1,0,1,0}, dy[4]={0,-1,0,1};//左下右上
char maze[MAX][MAX];
bool visit[MAX][MAX];

struct node{
    int x,y,path;
};
queue<node> qq;
int bfs(){
    int now_x = sx, now_y = sy;
    qq.push(node{sx,sy,0});
    while(!qq.empty()){
        now_x = qq.front().x;
        now_y = qq.front().y;
        if(maze[now_x][now_y]=='@')return qq.front().path;
        int front = qq.front().path+1;
        for(int i=0;i<4;i++){
            int nx = now_x+dx[i],ny = now_y+dy[i];
            if(!visit[nx][ny] && maze[nx][ny]!='#'){
                qq.push(node{nx,ny,front});
                visit[nx][ny] = 1;
            }
        }
        qq.pop();
    }
    return -1;
}
signed main(){
    ios;
    while(cin>>n && n!=0){
        memset(visit, 0, sizeof(visit));
        while(!qq.empty())qq.pop();
        for(int i=0;i<n;i++)cin>>maze[i];
        
        for(int i=0;i<n;i++)
            for(int j=0;j<strlen(maze[i]);j++)
                if(maze[i][j]=='K'){
                    sx = i;sy = j;
                    break;
                }
        int ans = bfs();
        if(ans>=0)cout<<ans<<"\n";
        else cout<<"= =\""<<"\n";
    }
    return 0;
}
```
