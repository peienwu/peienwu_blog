---
title: "[題解]CSES 2205 Gray Code"
date: 2021-1-3
tags: 
    - CSES
    - 題解
categories: CSES題解
mathjax: true
---

### Gray Code
<!--more-->
[題目連結](https://cses.fi/problemset/task/2205)

```cpp=
#include <bits/stdc++.h>
#define pb push_back
#define IOS ios::sync_with_stdio(0),cin.tie(0)
#define int long long
using namespace std;
int n;
vector<string> str;
 
void solve(){
    str.pb("0");str.pb("1");
    for(int i=0;i<n-1;i++){
        vector<string> temp1 = str,temp2 = str;
        reverse(temp2.begin(),temp2.end());
        str.clear();
        int len = temp1.size();
        for(int i=0;i<len;i++)temp1[i] = "0" + temp1[i];
        for(int i=0;i<len;i++)temp2[i] = "1" + temp2[i];
        str.insert(str.begin(),temp1.begin(),temp1.end());
        str.insert(str.end(),temp2.begin(),temp2.end());
    }
    for(auto i : str)cout<<i<<"\n";
}

signed main(){
    IOS;
    cin>>n;
    solve();
    return 0;
}
```
