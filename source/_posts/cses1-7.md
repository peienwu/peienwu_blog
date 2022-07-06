---
title: "[題解]CSES 1072 Two Knights"
date: 2021-1-3
tags: 
    - CSES
    - 題解
categories: CSES題解
mathjax: true
---

### Two Knights
<!--more-->
[題目連結](https://cses.fi/problemset/task/1072)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define N 10005
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n,dp[N];
 
void build(){
    memset(dp,0,sizeof(dp));
    for(int i=2;i<=10000;i++){
        int sum = (i*i)*(i*i-1)/2 - 4*(i-1)*(i-2);
        dp[i] = sum;
    }
}
 
signed main(){
    IOS;
    build();
    cin>>n;
    for(int i=1;i<=n;i++){
        cout<<dp[i]<<"\n";
    }
}
```
