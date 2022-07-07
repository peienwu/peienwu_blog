---
title: "[題解]NEOJ 141 取數字1"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 取數字1
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/141/)
**定義：**$dp[i]$ 為取第i個數字的的最大值
**轉移式：**$dp[i] = max(dp[i-2]+dp[i-2])+arr[i]$
可以發現到，因為$arr[i]$ 都是正數，因此加越多數字就代表最後的數值越大，而相鄰的兩項不能轉移，因此就要看$dp[i-2],dp[i-2]$其中i-4不用看是因為已經包含在i-2裡面了
**邊界：**$dp[1] = arr[1],dp[2] = arr[2],dp[3] = arr[1]+arr[3]$

```cpp=
#include <bits/stdc++.h>
using namespace std;
int t;

int main(){
    cin>>t;
    while(t--){
        int n,arr[100005];cin>>n;
        int dp[10005];
        memset(dp,0,sizeof(dp));
        memset(arr,0,sizeof(arr));

        for(int i=1;i<=n;i++)cin>>arr[i];
        dp[1] = arr[1];dp[2] = arr[2];dp[3] = arr[1]+arr[3];
        for(int i=4;i<=n+1;i++){
            dp[i] = max(dp[i-2],dp[i-3])+arr[i];
        }
        cout<<max(dp[n],dp[n-1])<<endl;
    }
}
```
