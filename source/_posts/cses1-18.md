---
title: "[題解]CSES 2431  Digit Queries"
date: 2021-1-3
tags: 
    - CSES
    - 題解
categories: CSES題解
mathjax: true
---

### Digit Queries
<!--more-->
[題目連結](https://cses.fi/problemset/task/2431)

```cpp=
#include <bits/stdc++.h>
#define N 20
#define int long long
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n,pref[N],p[20];

void init(){
    p[0] = 1;
    for(int i=1;i<19;i++){
        p[i] = p[i-1] * 10;
    }
    pref[1] = 9;
    for(int i=2;i<18;i++){
        pref[i] = p[i-1] * 9 * i;
    }
}

void solve(){
    int ind = 1;
    while(n > pref[ind])n -= pref[ind++];
    n--;                    //0-BASE
    int x = n / ind;        //0-BASE
    int y = n % ind;        //0-BASE
    int ans = p[ind-1] + x;
    string temp = to_string(ans);
    cout<<temp[y]<<"\n";
}

signed main(){
    IOS;
    init();
    int t;cin>>t;
    while(t--){
        cin>>n;
        solve();
    }
}
```
