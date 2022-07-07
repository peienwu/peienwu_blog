---
title: "[題解]NEOJ 157 高棕櫚農場"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 高棕櫚農場
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/157/)
這一題不能用重量做，因為重量的範圍可以到$10^5$，因此只能用價值來做
有一個要點，無限大可以memset定義為**0x3f3f3f3f**，以十進位表示1061109567，在int的範圍但不會超過

#### 定義

定義$f(n,m)$為取n樣物品,價值恰為m,重量總和最小值

#### 轉移方式

$f(n,m) = min(f(n-1,m), f(n-1,m-v_n)+w_n), m ≧ v_n$
$f(n,m) = f(n-1,m), m < v_n$

#### 邊界條件

f(0,0) = 0, f(0,k) = INF (k>0)$
因為取零樣物品價值要k不可能達到，因此重量設為無限大

我們可以藉由滾動dp來節省空間，壓成一維（跟用重量作為狀態一樣）
最後，在從dp裡面取出max(k), for all f(N,k) ≦ W

#### 程式碼

```cpp=
#include <bits/stdc++.h>
#define int long long
#define INF 0x3f3f3f3f
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int t;

signed main(){
    ios;
    cin>>t;
    while(t--){
        int n,m,val[105],weight[100005];cin>>n>>m;
        for(int i=1;i<=n;i++)cin>>weight[i]>>val[i];
        int dp[100005];
        //定義f(n,m)取n樣物品,價值為m,重量總和最小
        //dp[i]：價值為i時,重量最小為dp[i]
        memset(dp,INF,sizeof(dp));
        dp[0] = 0;
        for(int i=1;i<=n;i++){
            for(int j=10000;j>=val[i];j--){
                dp[j] = min(dp[j],dp[j-val[i]]+weight[i]);
            }
        }
        int ans = 0;
        for(int i=0;i<=10000;i++)
            if(dp[i] <= m && i > ans)ans = i;
        cout<<ans<<endl;
    }
}
```
