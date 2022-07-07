---
title: "[題解]NEOJ 142 取數字2"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---


### 取數字2
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/142/)
上面取數字1的進階版，可以一樣定義，不過轉移式要稍微改變（其實就只是把原本只有2的區間擴大成k）
**定義：**$dp[i]$ 為取第i個數字的的最大值
**轉移式：**
$dp[i]=max(arr[j]+arr[i]),2k\leq i \leq n,i-2k\leq j \leq i-k$
轉移式比較複雜一點，不過就是取數字1的延伸
**邊界：**
$dp[i] = arr[i],1\leq i \leq k$
$dp[i]=max(arr[j]+arr[i]),k+1\leq i \leq 2k,1\leq j \leq i-k$

邊界的話，如果要說成是轉移式也是可以啦

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0);
#define int long long
using namespace std;
int t;

signed main(){
    ios;
    cin>>t;
    while(t--){
        int n,arr[100005],k;cin>>n>>k;
        int dp[100005];
        memset(dp,0,sizeof(dp));
        memset(arr,0,sizeof(arr));

        for(int i=1;i<=n;i++)cin>>arr[i];
        //[1,k]的邊界轉移
        for(int i=1;i<=k;i++)dp[i] = arr[i];
        //[k+1,2k]的邊界轉移
        for(int i=k+1;i<=2*k;i++){
            for(int j=1;j<=i-k;j++){//看可以配上[1,k]最大的那一個
                dp[i]=max(dp[i],arr[j]+arr[i]);
            }
        }
        for(int i=2*k+1;i<=n;i++){
            for(int j = i-2*k;j<=i-k;j++){//開始轉移，掃描k次
                dp[i] = max(dp[i],dp[j]+arr[i]);
            }
        }
        cout<<max_element(dp, dp+n+1)<<endl;
    }
}
```
