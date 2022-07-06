---
title: "[題解]CSES 1092 Two Sets"
date: 2021-1-3
tags: 
    - CSES
    - 題解
categories: CSES題解
mathjax: true
---

### Two Sets
<!--more-->
[題目連結](https://cses.fi/problemset/task/1092)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define N 1000005
using namespace std;
int n;

void solve(){
    int total = n*(n+1)/2,sum = 0,cnt = 0;
    bool vis[N];memset(vis,0,sizeof(vis));
    if(total & 1){
        cout<<"NO"<<"\n";
        return;
    }
    cout<<"YES"<<"\n";
    for(int i=n;i>=1;i--){
        int next = sum + i;
        if(next <= total/2){
            sum += i;
            cnt++;
            vis[i] = 1;
        }
    }
    cout<<cnt<<"\n";
    for(int i=1;i<=n;i++){
        if(vis[i])cout<<i<<" ";
    }
    cout<<"\n"<<n - cnt<<"\n";
    for(int i=1;i<=n;i++){
        if(!vis[i])cout<<i<<" ";
    }
    cout<<"\n";
}

signed main(){
    cin>>n;
    solve();
    return 0;
}
```
