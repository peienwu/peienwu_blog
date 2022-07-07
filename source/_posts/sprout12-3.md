---
title: "[題解]NEOJ 793 想不到題目標題QQ"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 想不到題目標題QQ
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/793/)
圖中的-2改成-(k-1)，對於圖中的x,y都是設計成大數亂數，要承擔的風險就是這些大數相加之後不應該變成0的卻變成0（機率超小）
還有一個重點，對於每一筆詢問都要求出區間的和，因此要用O(n)維護前綴和！（一開始沒想到tle，不過靈光一閃維護前綴和就過了）
![](https://i.imgur.com/Z4NzA4J.png)

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define int long long int
#define N 500005
#define mod 1000000007
using namespace std;
int n,k,m,arr[N],times[N],fuck[N],value[N],pre[N];

void init(){
    memset(times, 0, sizeof(times));
    srand(time(NULL));
    pre[0] = 0;
    for(int i=0;i<=N;i++){
        int temp = rand()%mod;
        fuck[i] = temp;
    }
}

signed main(){
    ios;
    init();
    cin>>n>>k>>m;
    for(int i=1;i<=n;i++){
        cin>>arr[i];
        times[arr[i]]++;
        if(times[arr[i]]%k==0){
            value[i] = -(k-1)*fuck[arr[i]];
        }
        else{
            value[i] = fuck[arr[i]];
        }
        pre[i] = (pre[i-1]+value[i]);
    }

    for(int i=1;i<=m;i++){
        int l,r,sum=0;cin>>l>>r;
        sum = pre[r]-pre[l-1];
        if(sum==0)cout<<1;
        else cout<<0;
    }
    cout<<endl;
}
```
