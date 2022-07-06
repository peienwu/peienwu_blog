---
title: "[題解]CSES 2189 Point Location Test"
date: 2021-1-3
tags: 
    - CSES
    - 題解
categories: CSES題解
mathjax: true
---

### Point Location Test
<!--more-->
[題目連結](https://cses.fi/problemset/task/2189/)

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

signed main(){
    IOS;
    int t;cin>>t;
    while(t--){
        int x1,y1,x2,y2,x3,y3;
        cin>>x1>>y1>>x2>>y2>>x3>>y3;
        pt l = {x2 - x1,y2 - y1}, o = {x3 - x1,y3 - y1};
        
        if((l ^ o) == 0)cout<<"TOUCH"<<"\n";
        else if((l ^ o) > 0)cout<<"LEFT"<<"\n";
        else cout<<"RIGHT"<<"\n";
    }
}
```
