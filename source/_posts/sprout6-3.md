---
title: "[題解]NEOJ 124 王老先生有塊地喔"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 王老先生有塊地喔
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/124/)
這一題最需要關注的就是把問題縮小的關鍵
大範圍不會做，把規模縮到$2\times2$ 大小的方格總可以做了吧」
想辦法讓每一個$2\times2$ 大小的方格都有一格是被填滿的
剩下三格就很容易放進去
**注意：** 行跟列的維護很重要，一開始就要想好要怎麼樣定義儲存格，一維二維該放什麼

```cpp=
#include <bits/stdc++.h>
bool maze[1025][1025];

void DC(int,int,int);
void solve(int,int,int);

void solve(int N,int X,int Y){
    memset(maze, 0, sizeof(maze));
    maze[X][Y] = 1;
    DC(1,1,N);
}
void DC(int x,int y,int n){
    if(n<=1)return;
    int new_N = n/2,x_pos=0,y_pos=0;
    for(int i=x;i<x+n;i++){
        for(int j=y;j<y+n;j++){
            if(maze[i][j] == 1){
                x_pos = i;
                y_pos = j;
                break;
            }
        }
    }
    int pos;
    if(x_pos>=x+new_N){
        if(y_pos>=y+new_N)pos = 4;
        else pos = 3;
    }
    else{
        if(y_pos>=y+new_N)pos = 2;
        else pos = 1;
    }
    int nx = x+new_N,ny = y+new_N;
    switch (pos) {
        case 1:
            Report(nx, ny, nx-1, ny, nx, ny-1);
            maze[nx][ny] = 1;
            maze[nx-1][ny] = 1;
            maze[nx][ny-1] = 1;
            break;
        case 2:
            ny = ny-1;
            Report(nx, ny, nx-1, ny, nx, ny+1);
            maze[nx][ny] = 1;
            maze[nx-1][ny] = 1;
            maze[nx][ny+1] = 1;
            break;
        case 3:
            nx = nx-1;
            Report(nx, ny, nx+1, ny, nx, ny-1);
            maze[nx][ny] = 1;
            maze[nx+1][ny] = 1;
            maze[nx][ny-1] = 1;
            break;
        case 4:
            ny = ny-1;nx = nx-1;
            Report(nx, ny, nx+1, ny, nx, ny+1);
            maze[nx][ny] = 1;
            maze[nx+1][ny] = 1;
            maze[nx][ny+1] = 1;
            break;
        default:
            break;
    }
    DC(x, y, new_N);
    DC(x+new_N, y, new_N);
    DC(x, y+new_N, new_N);
    DC(x+new_N, y+new_N, new_N);
}
```
