---
title: "[題解]CSES 1069 Repetitions"
date: 2021-1-3
tags: 
    - CSES
    - 題解
categories: CSES題解
mathjax: true
---

### Repetitions
<!--more-->
[題目連結](https://cses.fi/problemset/task/1069)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
 
signed main(){
    IOS;
    string s;cin>>s;
    int len = s.size();
    int ans = 1,cur = 1;
    for(int i = 1;i < len;i++){
        if(s[i] == s[i-1])cur++;
        else cur = 1;
        ans = max(ans,cur);
    }
    cout<<ans<<"\n";
}
```
