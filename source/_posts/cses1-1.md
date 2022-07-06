---
title: "[題解]CSES 1068 Weird Algorithm"
date: 2021-1-3
tags: 
    - CSES
    - 題解
categories: CSES題解
mathjax: true
---

### Weird Algorithm
<!--more-->

[題目連結](https://cses.fi/problemset/task/1068)

```cpp=
#include <bits/stdc++.h>
using namespace std;
int n;
 
signed main(){
    cin>>n;
    while(n != 1){
        cout<<n<<" ";
        if(n % 2 == 1)n = 3 * n + 1;
        else n = n/2;
    }
    cout<<1<<"\n";
}
```
