---
title: "[題解]ZJ d978: 最长回文字串"
date: 2021-1-9
tags: 
    - 字串
    - 題解
categories: 字串題解
mathjax: true
---

### ZJ d978: 最长回文字串
<!-- more -->
[題目連結](https://zerojudge.tw/ShowProblem?problemid=d978)
> 題目敘述
題目即題意，給你一個字串，求出最長的回文字串長度為何？

一題LPS的裸題，用Manacher's Algorithm可以將時間複雜度做到 $O(n)$！

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld long double
#define N 500005
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int n,m,Z[2*N],t;
bool ans[2*N];
char S[2*N],T[N];
//T原字串、S插入點字串

void Longest(){
    n = strlen(T);m = 2*n+1;
    memset(S,'.',m);
    for(int i=0;i<n;i++)S[2*i+1] = T[i];
    Z[0] = 1;
    int l = 0,r = 0;
    for(int i=1;i<m;i++){
        Z[i] = max(min(Z[2*l-i],r-i),1);
        while(i-Z[i]>=0 && i+Z[i] < m && S[i+Z[i]]==S[i-Z[i]]){
            l = i;r = i+Z[i];
            Z[i]++;
        }
    }
}

signed main(){
    Orz;
    cin>>t;
    while(t--){
        cin>>T;
        Longest();
        int ans = 0;
        for(int i=0;i<m;i++){
            int z = (Z[i]-1);
            ans = max(ans,z);
        }
        cout<<ans<<endl;
    }
}
```
