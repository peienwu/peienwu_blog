---
title: "[題解]NEOJ 143 合成円円"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 合成円円
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/143/)
開始接觸到區間DP，比單純的DP又多了一些要注意的地方
上網查關於區間DP的資料，一般都會有這些內容：

```python=
for (枚舉區間長度)
    for (枚舉左端點)
        for (枚舉分割點)
            f[l, r] = f[l, k] + f[k, r] + val(l, r)
```

主要就是，從小的區間長度開始（大的區間需要小的區間的答案），枚舉左端點可以知道區間的範圍，接著再依序枚舉在區間內分割點，把長度為n的區間長度跑完之後就完成了
有了這一個$O(n^3)$ 的演算法，就可以開始來實作

**定義：**
$dp[i][j]$ $(1 \leq i,j \leq n)$ 為合併區間 $[i,j]$ 所需要的花費
**轉移式：**
$dp[i][j] = min(dp[i][k]+dp[k+1][j]+sum_{i,j}),i \leq k \leq j$
合併區間$dp[i][k],dp[k+1][j]$的花費為兩者相加再加上區間[i,j]的總和（可以用前綴維護）
**邊界：**
$dp[i][i] = 0,1 \leq i \leq n$

這個演算法的複雜度是$O(n^3)$，之後可以用dp優化的技巧做到更快

{% note success %}
**問題：這題可不可以用Greedy？**
我們如果每一次都找兩兩相鄰相加總和最小的兩個加起來，做n-1次，是不是可以找到最佳解？（如果可以，那幹嘛還要辛苦維護$n^3$的算法XD）

Q:答案是不行，那要反例何時出現？

> 10 7 6 7

這一組測資如果用Greedy做是63，用dp做是60，可以發現到，如果數字兩兩相加相等，那先加後加的順序就很重要，有可能因為順序不對（先加中間的7跟6），導致7沒辦法跟10合併產生更加的解法！

**找反例的方法：** 這一筆測資是利用隨機生成大量的數字去找出有沒有不一樣，方法by:
![](https://i.imgur.com/rPWTsXk.png)
{% endnote %}
這一題就先這樣，不過區間DP感覺就是可以很難的東西
時間複雜度$O(n^3)$

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
        int n,arr[105],pre[105];cin>>n;
        int dp[105][105];
        memset(pre, 0, sizeof(pre));
        memset(dp, 0x3f3f3f3f, sizeof(dp));
        for(int i=1;i<=n;i++){
            cin>>arr[i];
            pre[i] = pre[i-1]+arr[i];
            dp[i][i] = 0;
        }
        
        for(int len = 2;len<=n;len++){
            for(int i=1;i+len-1<=n;i++){//區間[i,j]長度為len，則j = i+len-1
                int j = i+len-1;
                for(int k=i;k<=j;k++){
                    dp[i][j] = min(dp[i][j],dp[i][k]+dp[k+1][j]+pre[j]-pre[i-1]);
                }
            }
        }
        cout<<dp[1][n]<<endl;
    }
}
```
