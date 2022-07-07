---
title: "[題解]NEOJ 138 円円數磁磚"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 円円數磁磚
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/138/)
我不會寫轉移式子啊...
首先可以觀察到，因為兩個磁磚的排法有3種，首先可以列出$f(n) = 3\times f(n-2)$ 這個式子，但觀察之後可以發現，如果排列的磁磚不能被兩兩分割，也可是一種新的排法。而對於固定的尺寸大小只會有一種排法，但因為可以顛倒放，因此視為兩種組合。
**轉移式：**
$f(n) = 3\times f(n-2)+2\times(f(n-4)+f(n-6)...)$
經過數學帶入消去：
$f(n-2) = 3\times f(n-4)+2\times(f(n-6)+f(n-8)...)$
$f(n) = 4\times f(n-2)-f(n-4)$

**邊界：**$dp[0] = 1, dp[2] = 3$

```cpp=
#include <bits/stdc++.h>
#define int unsigned long long
using namespace std;
int t;

signed main(){
    cin>>t;
    int dp[100005];
    memset(dp, 0, sizeof(dp));
    dp[0] = 1;dp[2] = 3;dp[3] = 0;
    for(int i=4;i<=100001;i+=2)dp[i] =(4*dp[i-2]-dp[i-4]+1000007)%1000007;
    while(t--){
        int n;cin>>n;
        cout<<dp[n]<<endl;
    }
}
```
