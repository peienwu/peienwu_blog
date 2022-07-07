---
title: "[題解]NEOJ 128 糟糕陣列"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 糟糕陣列
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/128/)
其中一種構造方式：對於第i列第i行的「糟糕陣列」，必須滿足聯集為 ${1,2,...,2k-1}$的數字，我們可以先構造出2*2的糟糕陣列，把它複製到右邊（加上邊長）、右下、與下方（加上邊長，複製完之後因為會有重複一個數字，扣掉就好
![](https://i.imgur.com/4G3GaOA.png)

```cpp=
#include <bits/stdc++.h>
using namespace std;
int maze[1026][1026];

void solve(int);
void DC(int n);

void solve(int N){
    memset(maze, 0, sizeof(maze));
    maze[1][1] = 1;
    maze[1][2] = 2;
    maze[2][1] = 3;
    maze[2][2] = 1;
    int temp = 4;
    while(temp<=N){
        DC(temp);
        temp = temp*2;
    }
    for(int i=1;i<=N;i++){
        for(int j=1;j<=N;j++){
            Report(maze[i][j]);
        }
    }
}

void DC(int n){
    int now_N = n/2;
    for(int i=1;i<=now_N;i++){
        for(int j=1;j<=now_N;j++){
            maze[i+now_N][j] = maze[i][j]+n;
            maze[i][j+now_N] = maze[i][j]+n;
            maze[i+now_N][j+now_N] = maze[i][j];
            if(maze[i][j]==1)maze[i][j+now_N]--;
        }
    }
}
```
