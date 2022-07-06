---
title: "[題解]CSES 2190 Line Segment Intersection"
date: 2021-1-3
tags: 
    - CSES
    - 題解
categories: CSES題解
mathjax: true
---

### Line Segment Intersection
<!--more-->
[題目連結](https://cses.fi/problemset/task/2190/)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define double long double
#define pii pair<int, int>
#define N 10000
#define x first
#define y second
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;

struct pt{
    int x,y;
    bool operator == (pt b){
        if(x == b.x && y == b.y)return true;
        return false;
    }
    pt operator - (pt b){return {x - b.x , y - b.y};}
    pt operator + (pt b){return {x + b.x , y + b.y};}
    int operator ^ (pt b){return (x * b.y - y * b.x);}
    int operator * (pt b){return (x * b.x + y * b.y);}
};

bool onseg(pt a,pt b,pt o){
    int cross = (a - o) ^ (b - o);
    int dot = (a - o) * (b - o);
    return cross == 0 && dot <= 0;
    //dot == 0 when o is a or b
}

int dir(pt a,pt b,pt o){
    int cross = (a - o) ^ (b - o);
    if(cross == 0)return 0;
    else if(cross > 0)return 1;
    else return -1;
}

bool inter(pt a,pt b,pt c,pt d){
    if(onseg(a,b,c) || onseg(a,b,d))return true;
    if(onseg(c,d,a) || onseg(c,d,b))return true;
    if((dir(a,b,c) * dir(a,b,d)) == -1
       && (dir(c,d,a) * dir(c,d,b)) == -1)
        return true;
    return false;
}

signed main(){
    IOS;
    int t;cin>>t;
    while(t--){
        pt pot[4];
        for(int i=0;i<4;i++)cin>>pot[i].x>>pot[i].y;
        if(inter(pot[0],pot[1],pot[2],pot[3]))cout<<"YES"<<"\n";
        else cout<<"NO"<<"\n";
    }
}
```
