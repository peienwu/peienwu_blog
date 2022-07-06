---
title: "[題解]CSES 2194 Minimum Euclidean Distance"
date: 2021-1-3
tags: 
    - CSES
    - 題解
categories: CSES題解
mathjax: true
---

### Minimum Euclidean Distance
<!--more-->
[題目連結](https://cses.fi/problemset/task/2194/)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define double long double
#define pii pair<int, int>
#define N 200005
#define INF LONG_LONG_MAX
#define x first
#define y second
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n;
vector <pii> pt;
 
int dis(pii a,pii b){
    int x = a.x - b.x,y = a.y - b.y;
    return x * x + y * y;
}
 
bool cmp(pii a,pii b){
    return a.y < b.y;
}
 
vector <pii> temp;
//區間[l,r]
int solve(int l,int r){
    if(l == r)return INF;
    int mid = (l + r) / 2,mid_x = pt[mid].x;
    int ans = min(solve(l,mid),solve(mid+1,r));
    
    temp.assign(r - l + 1,{0 , 0});
    merge(
          pt.begin() + l,pt.begin() + mid + 1,
          pt.begin() + mid + 1,pt.begin() + r + 1,
          temp.begin(), cmp
    );
    for(int i=l;i<=r;i++)pt[i] = temp[i-l];
    temp.clear();
    for(int i=l;i<=r;i++){
        if(abs(pt[i].x - mid_x)*abs(pt[i].x - mid_x) <= ans)temp.push_back(pt[i]);
    }
    int len = temp.size();
    for(int i = 0;i < len;i++){
        for(int j = i+1;j < len;j++){
            ans = min(ans,dis(temp[i],temp[j]));
            if(abs(temp[i].y - temp[j].y)*abs(temp[i].y - temp[j].y) > ans)break;
        }
    }
    return ans;
}
 
signed main(){
    IOS;
    cin>>n;
    pt.assign(n,{0,0});
    for(int i=0;i<n;i++)cin>>pt[i].x>>pt[i].y;
    sort(pt.begin(),pt.end());
    cout<<solve(0,n-1)<<"\n";
}
```
