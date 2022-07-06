---
title: "[題解]NEOJ 398 向量加法"
date: 2021-1-6
tags: 
    - 計算幾何
    - 題解
categories: 計算幾何題解
mathjax: true
---

### 向量加法
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/398/)
[Submission](https://neoj.sprout.tw/challenge/178462/)

> 題目敘述：
> 給你n個數字（0≤i<1，小數點精度到末九位），想知道到底有多少組 $(i,j,k)$ 滿足 $v_i+v_j=v_k$，其中 $i,j,k$ 可以重複。

這題其實跟計算幾何沒什麼關係，直接用unordered_map去做（有點像two sum，不過下面的code好像也不用開到multi），簡單！不過我在浮點數的地方吃了一些WA，最後算了直接改用字串處理這個惱人的東西XD

```cpp=
#include <bits/stdc++.h>
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define int long long
#define double long double
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define rep2(i,l,r) for(int i=l;i<r;i++)
#define pii pair<int,int>
#define x first
#define y second
#define eps (1e-9)
#define INF 1e10
#define N 2001
#define ll long long
#define ld long double
#define int long long
using namespace std;
int n;

signed main(){
    cin>>n;
    vector<int> vec(n);
    rep2(i,0,n){
        string s;cin>>s;
        int num = 0,times = 1000000000;
        for(int i=2;i<=10;i++){
            num += (s[i]-'0')*times;
            times/=10;
        }
        vec[i] = num;
    }
    unordered_multimap<int,int> mp;
    for(int i=0;i<n;i++){
        mp.insert({vec[i],i});
    }
    int ans = 0;
    for(int i=0;i<n;i++){
        for(int j=0;j<n;j++){
            ans += mp.count(vec[i] + vec[j]);
        }
    }
    cout<<ans<<endl;
}
```
