---
title: "[題解]CSES 2191 Polygon Area"
date: 2021-1-3
tags: 
    - CSES
    - 題解
categories: CSES題解
mathjax: true
---

### Polygon Area
<!--more-->
[題目連結](https://cses.fi/problemset/task/2191/)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define double long double
#define pii pair<int, int>
#define N 1005
#define x first
#define y second
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n;

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
}pot[N];

int solve(){
    int ans = 0;
    for(int i=0;i<n;i++)ans += (pot[i] ^ pot[i+1]);
    return ans;
}

signed main(){
    IOS;
    cin>>n;
    for(int i=0;i<n;i++)cin>>pot[i].x>>pot[i].y;
    pot[n] = pot[0];
    cout<<abs(solve())<<"\n";
}
```
