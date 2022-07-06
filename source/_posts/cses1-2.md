---
title: "[題解]CSES 1083 Missing Number"
date: 2021-1-3
tags: 
    - CSES
    - 題解
categories: CSES題解
mathjax: true
---

### Missing Number
<!--more-->
[題目連結](https://cses.fi/problemset/task/1083)

```cpp=
#include <bits/stdc++.h>
#define int long long
using namespace std;
int n;
 
signed main(){
    cin>>n;
    int sum = n*(n+1)/2;
    for(int i=0;i<n-1;i++){
        int temp;cin>>temp;
        sum -= temp;
    }
    cout<<sum<<endl;
}
```
