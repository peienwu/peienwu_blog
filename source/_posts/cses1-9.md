---
title: "[題解]CSES 1617 Bit Strings"
date: 2021-1-3
tags: 
    - CSES
    - 題解
categories: CSES題解
mathjax: true
---

### Bit Strings
<!--more-->
[題目連結](https://cses.fi/problemset/task/1617)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define N 1000005
#define mod 1000000007
using namespace std;
int n;

void solve(){
    int ans = 1;
    for(int i=0;i<n;i++){
        ans *= 2;
        ans = ans % mod;
    }
    cout<<ans<<"\n";
}

signed main(){
    cin>>n;
    solve();
    return 0;
}
```
