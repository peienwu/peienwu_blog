---
title: "[題解]CSES 1094 Increasing Array"
date: 2021-1-3
tags: 
    - CSES
    - 題解
categories: CSES題解
mathjax: true
---

### Increasing Array
<!--more-->
[題目連結](https://cses.fi/problemset/task/1094)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define N 200005
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n,arr[N];
 
signed main(){
    IOS;
    cin>>n;
    for(int i=0;i<n;i++)cin>>arr[i];
    
    int ans = 0;
    for(int i=1;i<n;i++){
        if(arr[i] < arr[i-1]){
            ans += arr[i-1] - arr[i];
            arr[i] = arr[i-1];
        }
    }
    cout<<ans<<"\n";
}
```
