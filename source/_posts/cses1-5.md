---
title: "[題解]CSES 1070 Permutations"
date: 2021-1-3
tags: 
    - CSES
    - 題解
categories: CSES題解
mathjax: true
---

### Permutations
<!--more-->
[題目連結](https://cses.fi/problemset/task/1070)

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
    bool vis[N];memset(vis,0,sizeof(vis));
    if(n == 2 || n == 3)cout<<"NO SOLUTION"<<endl;
    else if(n == 4)cout<<"2 4 1 3 ";
    else{
        for(int i=1;i<=n;i+=2){
            cout<<i<<" ";
            vis[i] = 1;
        }
        for(int i=1;i<=n;i++){
            if(vis[i] == 0)cout<<i<<" ";
        }
    }
    cout<<endl;
}
```
