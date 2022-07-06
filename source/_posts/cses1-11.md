---
title: "[題解]CSES 1754 Coin Piles"
date: 2021-1-3
tags: 
    - CSES
    - 題解
categories: CSES題解
mathjax: true
---

### Coin Piles
<!--more-->
[題目連結](https://cses.fi/problemset/task/1754)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define N 1000005
#define mod 1000000007
using namespace std;
int n;

void solve(){
    int a,b;cin>>a>>b;
    if(a > b)swap(a,b);
    if(b - a > a){
        cout<<"NO"<<"\n";
        return;
    }
    a = a % 3;b = b % 3;
    if(a > b)swap(a,b);
    if(a == 1 && b == 2)cout<<"YES"<<"\n";
    else if(a == 0 && b == 0)cout<<"YES"<<"\n";
    else cout<<"NO"<<"\n";
}

signed main(){
    int t;cin>>t;
    while(t--){
        solve();
    }
    return 0;
}
```
