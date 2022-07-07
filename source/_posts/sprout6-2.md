---
title: "[題解]NEOJ 789 好的序列"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 好的序列
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/789/)
題目簡單來說，就是給定n，要構造出一個1~n的序列，其中不包含3個數字以上的等差數列
**解題想法：** 我看不懂資芽的解法QQ
上網扣別人的扣，看幾乎都是同樣一個寫法，把奇數項的放前面，偶數項的放後面，之後不斷分治下去
直接實作然後AC ，於是我一直想到底要怎麼證明這個方法是正確的
於是我大概想到了一個上黑色白色的一個算合理的推倒吧
{% note success %}
首先，我們把長度為$n$的序列分別交錯塗上黑白
![](https://i.imgur.com/potNo28.png)
再來，把長度為$n$的序列畫一條線分成$[1,\dfrac{n}{2}]$ 與$[\dfrac{n}{2}+1,n]$兩個序列，把奇數次項的數字放入第一個序列，再把偶數次項的序列放入第二個
![](https://i.imgur.com/3oRNe5R.png)
我們可以發現，考慮橫跨中間線的兩邊是否有可能形成等差數列，可以發現是不可能，因為如果要橫跨兩邊，勢必要從一邊選出兩個，另一邊選一個，而同顏色的差為偶數，不同顏色的差必為奇數，因此**不可能構造出橫跨兩邊的等差數列**
但可以明顯發現，同一顏色是等差數列，其實只要分兩邊分別遞迴下去就可以了，遞迴處理完就是解答
![](https://i.imgur.com/EJTJh8I.png)
遞迴直到長度為2時就可以return 了
{% endnote %}

程式碼：

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define int long long
#define N 100005
using namespace std;
int ans = 0,n,arr[N],temp[N];

void DC(int l,int r){
    if(r-l<=1)return;
    int ind = l,mid = (l+r)/2;
    for(int i=l;i<=r;i++)temp[i] = arr[i];
    for(int i=l;i<=r;i+=2)arr[ind++] = temp[i];
    for(int i=l+1;i<=r;i+=2)arr[ind++] = temp[i];
    DC(l, mid);
    DC(mid+1, r);
}
signed main(){
    ios;
    cin>>n;
    for(int i=1;i<=n;i++)arr[i] = i;
    DC(1, n);
    for(int i=1;i<=n;i++)cout<<arr[i]<<" ";
    cout<<endl;
}
```
