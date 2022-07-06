---
title: "[題解]CSES 1622 Creating Strings"
date: 2021-1-3
tags: 
    - CSES
    - 題解
categories: CSES題解
mathjax: true
---

### Creating Strings
<!--more-->
[題目連結](https://cses.fi/problemset/task/1622)

```cpp=
#include <bits/stdc++.h>
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n;

signed main(){
    IOS;
    string s;cin>>s;
    int len = s.size(),num[30];
    memset(num,0,sizeof(num));
    for(int i=0;i<len;i++){
        num[s[i] - 'a'] += 1;
    }
    string str;
    for(int i = 0;i < 26;i++){
        while(num[i]){
            str += (char)(i + 'a');
            num[i]--;
        }
    }
    vector<string> ans;
    do{
        ans.push_back(str);
    }while(next_permutation(str.begin(),str.end()));
    
    cout<<ans.size()<<"\n";
    for(auto i : ans)cout<<i<<"\n";
    return 0;
}
```
