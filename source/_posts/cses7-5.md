---
title: "[題解]CSES 2193 Polygon Lattice Points"
date: 2021-1-3
tags: 
    - CSES
    - 題解
categories: CSES題解
mathjax: true
---

### Polygon Lattice Points
<!--more-->
[題目連結](https://cses.fi/problemset/task/2193/)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define double long double
#define pii pair<int, int>
#define N 100005
#define INF 1000005
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

int solve(){
    int sum = 0;
    for(int i=0;i<n;i++)sum += pt[i] ^ pt[i+1];
    return abs(sum);
}

int gcd(int a,int b){
    if(b == 0)return a;
    return gcd(b,a%b);
}

signed main(){
    IOS;
    cin>>n;
    for(int i=0;i<n;i++)cin>>pt[i].x>>pt[i].y;
    pt[n] = pt[0];
    int cnt = 0,ans = 0;
    for(int i=0;i<n;i++)
        cnt += gcd(abs(pt[i].x-pt[i+1].x),abs(pt[i].y-pt[i+1].y));
    ans = (solve() + 2 - cnt)/2;
    cout<<ans<<" "<<cnt<<"\n";
}
```
