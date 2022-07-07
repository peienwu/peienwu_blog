---
title: "[題解]NEOJ 73 東方古墓古文"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 東方古墓古文
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/73)
這一題主要是二分搜工作量上限X，寫一個函數判斷是否工作量X是否可行
Debug 超久，不知道在幹嘛，結果最後是卡在二分搜的上界範圍不夠大
要開到$10^9$這麼大！
時間複雜度：$N$$log$(總工作量)

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0);
#define int long long
using namespace std;
int t;
int n,m,arr[100005];

bool is_ok(int lim){
    int sum = 0,ans = 1;
    for(int i=0;i<n;i++){
        if(arr[i]>lim)return 0;
        sum += arr[i];
        if(sum+arr[i+1] > lim){
            sum = 0;
            ans++;
        }
    }
    if(ans>m)return 0;
    else return 1;
}

signed main(){
    ios;
    cin>>t;
    while(t--){
        cin>>n>>m;
        memset(arr, 0, sizeof(arr));
        for(int i=0;i<n;i++)cin>>arr[i];
        arr[n] = -INT_MAX;
        int r = 1000000001,l = 0;
        while(r-l > 1){
            int mid = (l+r)/2;
            if(is_ok(mid))r = mid;
            else l = mid;
        }
        if(r == 1000000001)cout<<1000000000<<endl;
        else cout<<r<<endl;
    }
}
```
