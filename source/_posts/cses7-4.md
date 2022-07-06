---
title: "[題解]CSES 2192 Point in Polygon"
date: 2021-1-3
tags: 
    - CSES
    - 題解
categories: CSES題解
mathjax: true
---

### Point in Polygon
<!--more-->
[題目連結](https://cses.fi/problemset/task/2192/)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define double long double
#define pii pair<int, int>
#define N 1005
#define INF 1000000005
#define x first
#define y second
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n,m;

struct point{
    int x,y;
    bool operator == (point b){
        if(x == b.x && y == b.y)return true;
        return false;
    }
    point operator - (point b){return {x - b.x , y - b.y};}
    point operator + (point b){return {x + b.x , y + b.y};}
    int operator ^ (point b){return (x * b.y - y * b.x);}
    int operator * (point b){return (x * b.x + y * b.y);}
}pt[N];

bool onseg(point a,point b,point o){
    int cross = (a - o) ^ (b - o);
    int dot = (a - o) * (b - o);
    return (cross == 0) && (dot <= 0);
}

int dir(point a,point b,point o){
    int cross = (a - o) ^ (b - o);
    if(cross == 0)return 0;
    else if(cross > 0)return 1;
    else return -1;
}

bool inter(point a,point b,point c,point d){
    if(onseg(a,b,c) || onseg(a,b,d))return true;
    if(onseg(c,d,a) || onseg(c,d,b))return true;
    if(dir(a,b,c) * dir(a,b,d) < 0 && dir(c,d,a) * dir(c,d,b) < 0)
        return true;
    return false;
}

int solve(point cur){
    int sum = 0;
    for(int i=0;i<n;i++){
        if(onseg(pt[i],pt[i+1],cur) == 1)return -1;
        if(inter(pt[i],pt[i+1],cur,point{INF,cur.y}))sum++;
        point temp = pt[i].y > pt[i+1].y ? pt[i] : pt[i+1];
        if(temp.y == cur.y && temp.x > cur.x)sum--;
    }
    return sum;
}

signed main(){
    IOS;
    cin>>n>>m;
    for(int i=0;i<n;i++)cin>>pt[i].x>>pt[i].y;
    pt[n] = pt[0];
    for(int i=0;i<m;i++){
        point temp;cin>>temp.x>>temp.y;
        int ans = solve(temp);
        if(ans == -1)cout<<"BOUNDARY"<<"\n";
        else if(ans & 1)cout<<"INSIDE"<<"\n";
        else cout<<"OUTSIDE"<<"\n";
    }
}
```
