---
title: "[題解]NEOJ 416 玩電梯"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 玩電梯
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/416/)
[題目連結2](https://codeforces.com/problemset/problem/479/E)

這一題要用到3個重要的技巧：前綴和、差分、滾動dp
差分在某一次手寫作業有寫到，不過那時候沒有很注意這個部分就是了
{% note info %}
**差分**

差分是前綴和的逆運算，也就是說，把兩項的差算出來就是差分。定義如下：
$$b_i = \begin{cases}a_i-a_{i-1}, &\text{if }i\gt 1 \\a_1, & \text{if } i = 1\end{cases}$$

差分的使用時機是區間加值，一個區間內的數字都加上一個定值，這時候就可以使用到差分的技巧。使用方式如下，當我要在區間 $[l,r]$ 的每一個數字都加上一個值$v$，以下步驟：

1. 定義一個新的陣列 $b_i$ 表示每一項差分
2. 設 $b_l = b_l+v$，$b_{r+1} = b_{r+1}-v$
3. 將差分的每一項加上前一項，即為原數列 $b_i = b_{i-1}+b_i$

第二步驟可以重複好幾次做，這樣複雜度從原本的$O(n)$就變成了O(1)了！
{% endnote %}

這一題使用到差分的技巧，讓原本的$O(kn^2)$減少成$O(kn)$，然後就可以過了！

#### 定義

定義 $dp[i][j]$ 為第 i 次走到樓層j的方法數

#### 轉移式

這題如果用拉的比較不好想，所以改用推的試試看
$dp[i+1][j] = dp[i+1][j]+dp[i][p],$ for $j\in[p-r],[p+1,p+r],r = |p-b|-1$

#### 邊界條件

$dp[0][a] = 1$

轉移式比較複雜一點，不過可以用差分優化搭配前綴和把原本$O(n)$的時間降到$O(1)$
從這一題可以發現到，用拉的和用推的有不同的使用時機，可以以思考方式比較清楚的想法去想轉移式。

```cpp=
#include <bits/stdc++.h>
#define int long long
#define mod 1000000007
#define ios ios::sync_with_stdio(0)
using namespace std;
int n,a,b,k,dp[2][2005];

int modify(int x){
    return (x%mod+mod)%mod;
}

void sec(int l,int r,int v,int id){
 
    l = max(l,(long long)1);
    r = min(r,n);
    dp[(id+1)%2][l] = modify(dp[(id+1)%2][l]+v);
    dp[(id+1)%2][r+1] = modify(dp[(id+1)%2][r+1]-v);
}

signed main(){
    ios;
    cin>>n>>a>>b>>k;

    memset(dp,0,sizeof(dp));

    dp[0][a] = 1;
    for(int s=0;s<k;s++){  //每一次電梯移動
        for(int i=1;i<=n;i++){ //每一樓層轉移
            int d = abs(b-i)-1;
            sec(i-d,i-1,dp[s%2][i],s);
            sec(i+1,i+d,dp[s%2][i],s);
        }
        for(int i=1;i<=n;i++){
            dp[(s+1)%2][i] += dp[(s+1)%2][i-1];
            dp[(s+1)%2][i] = modify(dp[(s+1)%2][i]);
        }
        for(int i=1;i<=n;i++)dp[(s)%2][i] = 0;
    }

    int ans = 0;
    for(int i=1;i<=n;i++){
        ans+=dp[k%2][i];
        ans = modify(ans);
    }
    cout<<ans<<endl;
}

//8樓、2樓開始、5樓不能去、2次電梯
```
