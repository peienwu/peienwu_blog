---
title: "[題解]NEOJ 266 溫力的故事"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 溫力的故事
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/266/)
給字串集合A，接下來給m個字串，問你在m個字串中i字串一共出現了幾次。
直覺的想法就是直接用multiset來解這一題。

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define int unsigned long long int
#define N 105
using namespace std;
int n,m,power[N],C = 13331;

void init(){
    power[0] = 1;
    for(int i=1;i<=N;i++){
        power[i] = power[i-1]*C;
    }
}

int func(string s){
    int len = s.size(),sum = 0;
    for(int i=1;i<=len;i++){
        sum+=((int)s[i-1]*power[len-i]);
    }
    return sum;
}

signed main(){
    ios;
    init();
    multiset<int> ss;
    cin>>n>>m;
    for(int i=0;i<n;i++){
        string s;cin>>s;
        int temp = func(s);
        ss.insert(temp);
    }
    for(int i=0;i<m;i++){
        string s;cin>>s;
        int temp = func(s);
        cout<<ss.count(temp)<<endl;
    }
}
```
