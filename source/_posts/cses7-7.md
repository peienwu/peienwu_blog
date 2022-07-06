---
title: "[題解]CSES 2195 Convex Hull"
date: 2021-1-3
tags: 
    - CSES
    - 題解
categories: CSES題解
mathjax: true
---

### Convex Hull
<!--more-->
[題目連結](https://cses.fi/problemset/task/2195/)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define double long double
#define pii pair<int, int>
#define N 200005
#define INF LONG_LONG_MAX
#define x first
#define y second
#define all(a) a.begin(),a.end()
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
};
vector<point> pt;

bool cmp(point a,point b){
    if(a.x == b.x)return a.y < b.y;
    return a.x < b.x;
}

bool dir(point a,point b,point c){
    point A = a - c,B = b - c;
    return (A ^ B) >= 0;
}

vector<point> convex(){
    vector<point> h;
    sort(all(pt),cmp);
    for(auto i : pt){
        while(h.size() > 1 && !dir(h[h.size()-1],i,h[h.size()-2]))
            h.pop_back();
        h.push_back(i);
    }
    int down = h.size();
    h.pop_back();
    reverse(all(pt));
    for(auto i : pt){
        while(h.size() > down && !dir(h[h.size()-1],i,h[h.size()-2]))
            h.pop_back();
        h.push_back(i);
    }
    h.pop_back();
    return h;
}

signed main(){
    IOS;
    cin>>n;
    pt.assign(n,{0,0});
    for(int i=0;i<n;i++)cin>>pt[i].x>>pt[i].y;
    vector<point> ans = convex();
    cout<<ans.size()<<"\n";
    for(auto i : ans)cout<<i.x<<" "<<i.y<<"\n";
}
```
