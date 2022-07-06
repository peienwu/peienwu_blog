---
title: "[題解]CSES 1071 Number Spiral"
date: 2021-1-3
tags: 
    - CSES
    - 題解
categories: CSES題解
mathjax: true
---

### Number Spiral
<!--more-->
[題目連結](https://cses.fi/problemset/task/1071)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define N 1000005
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n;
 
signed main(){
    IOS;
    cin>>n;
    while(n--){
        int x,y;cin>>x>>y;
        int ly = max(x,y)-1,st = ly*ly;
        if(!(ly % 2)){
            if(y < ly+1)cout<<st+y<<endl;
            else cout<<(st+ly+1)+(ly+1-x)<<endl;
        }
        else{
            if(x < ly+1)cout<<st+x<<endl;
            else cout<<(st+ly+1)+(ly+1-y)<<endl;
        }
    }
}
```
