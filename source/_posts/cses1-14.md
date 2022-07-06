---
title: "[題解]CSES 2165 Tower of Hanoi"
date: 2021-1-3
tags: 
    - CSES
    - 題解
categories: CSES題解
mathjax: true
---

### Tower of Hanoi
<!--more-->
[題目連結](https://cses.fi/problemset/task/2165)

```cpp=
#include <bits/stdc++.h>
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n;

void solve(int from,int to,int by,int n){
    if(n < 1)return;
    solve(from,by,to,n-1);
    cout<<from<<" "<<to<<"\n";
    solve(by,to,from,n-1);
}

signed main(){
    IOS;
    cin>>n;
    cout<< (1<<n)-1 <<"\n";
    solve(1,3,2,n);
    return 0;
}
```
