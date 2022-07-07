---
title: "[題解]NEOJ 42 庭院裡的水池"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 庭院裡的水池
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/42/)
看圖形有幾個連通圖的判斷的水題，可以用dfs 或bfs 做，小心index不要戳到負的

**BFS作法**

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int t;
bool maze[1005][1005],visit[1005][1005];
int dx[4]={-1,0,1,0}, dy[4]={0,1,0,-1};
queue<int> qx,qy;

void bfs(int x,int y){
    int now_x=x,now_y=y;
    qx.push(x);
    qy.push(y);
    while(!qx.empty()){
        now_x = qx.front();now_y = qy.front();
        visit[now_x][now_y]=1;
        for(int i=0;i<4;i++){
            int nx = now_x+dx[i], ny = now_y+dy[i];
            if(!visit[nx][ny] && maze[nx][ny]==1){
                qx.push(nx);
                qy.push(ny);
                visit[nx][ny] = 1;
            }
        }
        qx.pop();
        qy.pop();
    }
}
int main(){
    ios;
    cin>>t;
    while(t--){
        int a,b;cin>>a>>b;
        memset(maze,0,sizeof(maze));
        memset(visit,0,sizeof(visit));
        for(int i=1;i<=a;i++){
            for(int j=1;j<=b;j++){
                char temp;cin>>temp;
                if(temp=='#')maze[i][j]=0;
                else maze[i][j]=1;
            }
        }
        int ans = 0;
        for(int i=1;i<=a;i++){
            for(int j=1;j<=b;j++){
                if(maze[i][j]==1 && visit[i][j]==0){
                    bfs(i,j);
                    ans++;
                }
            }
        }
        cout<<ans<<endl;
    }
}
```

**DFS作法**

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int t;
bool maze[1005][1005],visit[1005][1005];
int dx[4]={-1,0,1,0}, dy[4]={0,1,0,-1};
queue<int> qx,qy;

void dfs(int x, int y){
    visit[x][y] = 1;
    for(int i=0;i<4;i++){
        int nx = x+dx[i], ny = y+dy[i];
        if(!visit[nx][ny] && maze[nx][ny]==1){
            dfs(nx, ny);
        }
    }
}
int main(){
    ios;
    cin>>t;
    while(t--){
        int a,b;cin>>a>>b;
        memset(maze,0,sizeof(maze));
        memset(visit,0,sizeof(visit));
        for(int i=1;i<=a;i++){
            for(int j=1;j<=b;j++){
                char temp;cin>>temp;
                if(temp=='#')maze[i][j]=0;
                else maze[i][j]=1;
            }
        }
        int ans = 0;
        for(int i=1;i<=a;i++){
            for(int j=1;j<=b;j++){
                if(maze[i][j]==1 && visit[i][j]==0){
                    dfs(i, j);
                    ans++;
                }
            }
        }
        cout<<ans<<endl;
    }
}
```

比較上面兩種方法的複雜度
![](https://i.imgur.com/WfcJ5I8.png)
BFS明顯優於DFS 啊！（遞迴本身來說就很耗記憶體?!應該是這題特殊的原因，不然應該用DFS 較優）
