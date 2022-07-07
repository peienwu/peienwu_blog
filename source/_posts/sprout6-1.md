---
title: "[題解]NEOJ 125 逆序數對"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---


### 逆序數對
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/125/)
這是要求逆序數對的兩個數字的和，總共卡了兩個地方：

1. TLE 計算和的複雜度爆了
2. WA 數字溢位後爆了

第一點的解決方式：對於每一層利用O(N)的時間算前墜和
第二點的解決方式：每做完一個運算就mod 一次

```cpp=
#include <bits/stdc++.h>
#define int long long
#define N 1000005
using namespace std;
int ans = 0,n,arr[N],pre[N];

void merge_sort(int l,int r){
    if(r-l<=1)return;
    int mid = (l+r)/2;
    merge_sort(l,mid);
    merge_sort(mid,r);
    
    int nl = l,nr = mid,ind = 0,temp[r-l];
    pre[l-1] = 0;
    for(int i=l;i<=mid;++i)pre[i] = pre[i-1]+arr[i];
    for(;nl<mid;nl++,ind++){
        while(arr[nr]<arr[nl] && nr<r){
            int times = mid-nl;//逆序數對的數量
            ans+=pre[mid-1]-pre[nl-1];
            ans+=arr[nr]*times;
            ans = ans%10000019;
            temp[ind] = arr[nr];
            nr++;ind++;
        }
        temp[ind] = arr[nl];
    }
    while(nr<r){//把剩下沒放的放進去
        temp[ind] = arr[nr];
        nr++;ind++;
    }
    for(int i=0;i<r-l;++i)arr[i+l] = temp[i];
}
signed main(){
    cin>>n;
    memset(arr, 0, sizeof(arr));
    for(int i=1;i<=n;++i)scanf("%lld",&arr[i]);
    merge_sort(1,n+1);
    printf("%lld\n",ans%10000019);
}
```
