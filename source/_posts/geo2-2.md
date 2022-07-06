---
title: "[題解]NEOJ 399 等長線段對"
date: 2021-1-6
tags: 
    - 計算幾何
    - 題解
categories: 計算幾何題解
mathjax: true
---

### 等長線段對
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/399/)
[Submission](https://neoj.sprout.tw/challenge/178471/)
> 題目敘述：
> 給定平面上很多個點，求出有幾對線段等長（輸入有重複的點）。

既然n≤500，那就直接枚舉吧，沒啥特別難度。

```cpp=
#include <bits/stdc++.h>
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define rep(i,a,b) for(int i=a,i<=b;i++)
#define rep2(i,a,b) for(int i=a;i<b;i++)
#define pii pair<int,int>
#define pdd pair<double,double>
#define int long long
#define ll long long
#define ld long double
#define N 1003
#define eps 1e-9
#define x first
#define y second
using namespace std;
int n;
pii p[N];

int dist(pii a,pii b){
    int x = a.x-b.x,y = a.y-b.y;
    return x*x+y*y;
}

signed main(){
    Orz;
    cin>>n;
    rep2(i,0,n)cin>>p[i].x>>p[i].y;
    map<int,int>mp;
    for(int i=0;i<n;i++){
        for(int j=i+1;j<n;j++){
            int dis = dist(p[i],p[j]);
            mp[dis]+=1;
        }
    }
    int ans = 0;
    for(auto i:mp){
        int cnt = i.second;
        ans+=((cnt*(cnt-1))/2);
    }
    cout<<ans<<endl;
}
```
