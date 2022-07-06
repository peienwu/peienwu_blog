---
title: "[題解]NEOJ 792 遊戲：最終回"
date: 2021-1-6
tags: 
    - 計算幾何
    - 題解
categories: 計算幾何題解
mathjax: true
---

### 遊戲：最終回
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/792/)
[Submission](https://neoj.sprout.tw/challenge/178786/)
> 題目敘述
共有n個二維平面上的格子點，這些點會形成簡單多邊形。試求或在簡單多邊形內部的格線總長（包括垂直與水平格線）。

這邊有一個不嚴謹的推導方式，不過他是正確的。令多邊形內部格線長度為S，多邊形的邊落在的格線長度為T，多邊形面積T，則有以下關係式：

$$S = 2A-\frac{T}{2}$$

詳細的公式推導可以可以參閱下圖，平行四邊形（斜線部分）內部**垂直**的格線長度為： 大矩形 $(x_1+x_2)(y_1+y_2)$ 扣掉左右上下共四個三角形兩兩拼成一個矩形 $x_1y_1$ 以及 $x_2y_2$，還有左上右下兩個正方形 $2x_2y_1$，整理之後會發現其實跟面積是一樣的。對於垂直部分也是類似的情況。

![](https://i.imgur.com/wemDPMp.jpg)

好像隱約發現到面積與格線長度有十分密切的關係，算出面積，把在格線上的邊進行特判扣掉，就可以得到格線長度。

這一題我想了很久，一直看不出來關係式到底長怎樣，直到大神提點才發現原來有這樣的關係，我反應好遲鈍:cry:

![](https://i.imgur.com/Fy1wSky.png)

以下是AC Code：

```cpp=
#include <bits/stdc++.h>
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define rep(i,a,b) for(int i=a;i<=b;i++)
#define pii pair<int,int>
#define pdd pair<double,double>
#define int long long
#define ll long long
#define ld long double
#define N 100001
#define all(x) x.begin(),x.end()
#define eps 1e-9
#define x first
#define y second

using namespace std;

struct pt{
    int x,y;
    bool operator < (pt b){
        if(x == b.x)return y < b.y;
        return x < b.x;
    }
    bool operator > (pt b){
        if(x == b.x)return y > b.y;
        return x > b.x;
    }
    bool operator == (pt b){
        if(x-b.x == 0 && y-b.y == 0)return true;
        return false;
    }
    pt operator+(pt b) {return {x + b.x, y + b.y};} //向量相加
    pt operator-(pt b) {return {x - b.x, y - b.y};} //向量相減
    int operator^(pt b) {return x * b.y - y * b.x;} //向量外積cross
    int operator*(pt b) {return x * b.x + y * b.y;} //向量內積dot
};

vector<pt> p;
vector<int>  cnt;
int n,edge,ans;

int solve(){
    int area = 0;
    rep(i,0,n-1){
        area += (p[i]^p[i+1]);
        if(p[i].y == p[i+1].y)edge += abs(p[i].x-p[i+1].x);
        if(p[i].x == p[i+1].x)edge += abs(p[i].y-p[i+1].y);
    }
    area = abs(area);
    return area;
}

signed main(){
    Orz;
    cout<<fixed<<setprecision(1);
    while(cin>>n){
        p.assign(n+1,{0,0});
        edge = 0;
        rep(i,0,n-1)cin>>p[i].x>>p[i].y;
        p[n] = p[0];
        ans = solve();
        cout<<ans-((ld)edge/2)<<endl;
    }
}
```
