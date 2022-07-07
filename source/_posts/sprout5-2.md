---
title: "[題解]NEOJ 74 円円攻略黃河(TIOJ)"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---
### 円円攻略黃河(TIOJ)
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/74/)
這一題要求最長的長短相間的序列長度，雖然知道是Greedy ，但第一時間想不到具體的作法
卡了有一點久，總共嘗試了三次，前兩次以為對，但WA 後還是發現其中的Bug

1. 從第一個數字開始，找到下一個比自己小的數字，再找比那一個大的，重複幾次直到結束
{% note info %}
問題在於，第一個數字可能很小，導致接下來的序列長度很短
{% endnote %}
2. 找到第一個「下一個數比自己小」的數字，接著重複第1點的步驟
{% note info %}
因為要保證序列是最長的，會希望在找比自己大或小的數字時可以盡量最大或最小，才可以確保所有數字能有機會被包含進去
{% endnote %}
3. 對每一個數字如果要找大於自己的，就一直繼續往上找直到最大，如果要找最小的也是一樣
{% note info %}
以 *5 1 2 4 5 3 6* 來說，就是從5開始，接下來是1（最小了），接下來往上找，可以找到2, 4, 5，而為了盡量讓數字大才能包到接下來的數字（如果選2下一個就不能是3），所以選5，再來就是3 跟6 。
所以序列為： *5 1 5 3 6* ， 長度為5
{% endnote %}
想說應該要AC 了，沒想到沒有memset 所以吃了一個WA ！

```cpp=
#include <bits/stdc++.h>
#define int long long
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int t;

signed main(){
    ios;
    cin>>t;
    int n,arr[1000005];
    while(t--){
        cin>>n;
        memset(arr,0,sizeof(arr));
        for(int i=0;i<n;i++)cin>>arr[i];
        int height = 0, ind = 0, ans = 1;//0->down 1->up
        while(ind<n && arr[ind]<=arr[ind+1])ind++;
        
        while(ind<n){
            if(height == 0 && arr[ind]!=arr[ind+1]){
                while(arr[ind]>=arr[ind+1])ind++;
                height = 1;
            }
            else if(height == 1 && arr[ind]!=arr[ind+1]){
                while(arr[ind]<=arr[ind+1])ind++;
                height = 0;
                ans+=2;
            }
            ind++;//如果有連續相同數字需要加
        }
        cout<<ans<<endl;
    }
}
```
