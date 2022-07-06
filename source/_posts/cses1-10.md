---
title: "[題解]CSES 1618 Trailing Zeros"
date: 2021-1-3
tags: 
    - CSES
    - 題解
categories: CSES題解
mathjax: true
---

### Trailing Zeros
<!--more-->
[題目連結](https://cses.fi/problemset/task/1618)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define N 1000005
#define mod 1000000007
using namespace std;
int n;

void solve(){
    int f = 5,ans = 0;
    for(int i=0;i<12;i++){
        ans += n / f;
        f *= 5;
    }
    cout<<ans<<"\n";
}

signed main(){
    cin>>n;
    solve();
    return 0;
}
```
