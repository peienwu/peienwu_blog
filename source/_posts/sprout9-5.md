---
title: "[題解]NEOJ 185 烏龜疊疊樂---易"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 烏龜疊疊樂---易
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/185/)
這題簡單來說，是給定一個數列，要嘗試分配成好幾堆，每一堆的數量不超過k個。其中第i堆的總和乘上(i-1)，算出來的總和要最小。由題目敘述可以知道，每一堆到後面乘上的數字是越來越大，也就是被加上的次數會越來越多，如果我們將數列倒過來dp會比正面做來得容易維護。

{% note success %}

**轉移式**
dp[i] = max(dp[j]+pref[j]),for i-k≤j≤i-1，pref是維護已經反序過後的數列之前綴和。以範測為例，當k=2時，dp[i]可以選擇的就是從dp[i-1]跟dp[i-2]轉移，如果選擇dp[i-2]就表示第i項會和第i-1項合併成一堆，加上pref[i-2]也就是把前面的每一堆的數量乘上的數字都加一，也就是加上一個前綴和（由於每一個數字只會被合併一次）

**小證明：每一個數字只會被合併一次**
在轉移的過程中，有沒有可能會發生dp[i]要轉移的對象是dp[i-2]，但dp[i-1]卻已經被融合過了，造成三個烏龜融合在一起的情況？

也就是下圖這一種情況，dp[i-1]、dp[i-2]合併，dp[i]、dp[i-1]合併，變成三個融合超過k的情況（dp[i-2],dp[i-1],dp[i]合併）。
![](https://i.imgur.com/3NufpZ9.png)
答案：不會

首先先從dp[i-3]轉移的dp[i-1]表示第i-2跟i-1是合併成一堆，接下來的dp[i]從dp[i-2]轉移，加上了pref[i-2]，也就是把i-2獨立出去，i跟i-1合併成一堆，因此不會有大於k個烏龜合併在一起的情況。
{% endnote %}

這一題的轉移式不好想，要把東西反序之後比較好dp，轉移式也比較好寫出來！觀察到越後面的數字被重複加的機會比較大（高度越高），因此將數列的順續顛倒搭配前綴和就能比較方便的轉移！

#### 定義

$dp[i]$定義為使用1到i個烏龜，違和度的最大值

#### 轉移式

$dp[i] = max(dp[j]+pref[j]),for\ i-k≤j≤i-1$
這個轉移式是建立在順序顛倒的情況，在輸入的時候順便把逆序。

#### 邊界

$for\ each\ dp[i] = 0$

```cpp=
#include <bits/stdc++.h>
#define int long long
#define N 500005
#define ios ios::sync_with_stdio(0)
#define FOR(i,n) for(int i=0;i<n;i++)
using namespace std;
int n,k,arr[N],pref[N];

signed main(){
    ios;
    cin>>n>>k;
    for(int i=n;i>0;i--)cin>>arr[i];
    for(int i=1;i<=n;i++)pref[i] = pref[i-1]+arr[i];
    
    int dp[N];
    memset(dp,0,sizeof(dp));
    
    deque<pair<int,int>> deq;
    deq.push_back(make_pair(0,0));
    
    for(int i=1;i<=n;i++){
        while(!deq.empty() && deq.front().second<i-k)deq.pop_front();
        dp[i] = deq.front().first;
        while(!deq.empty() && deq.back().first <= dp[i]+pref[i])deq.pop_back();
        deq.push_back(make_pair(dp[i]+pref[i],i));
    }
    cout<<dp[n]<<endl;
}
```
