---
title: "[題解]NEOJ 188 円円賣漢堡"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 円円賣漢堡
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/188/)
首先在做任何優化之前，都必須先列出定義與轉移式。

#### 定義

定義$dp[i]$為在第1到i個點開店，且第i個點有開店時的最大盈利

#### 轉移式

$dp[i] = val[i]+max(dp[j]-c(i-j)),i-k≤j≤i-1$

#### 邊界

$dp[i] = 0, 1≤i≤n$

做完以上的定義與轉移式之後，可以得到複雜度：$O(NK)$就可以得到以下的結果：
![](https://i.imgur.com/Z4OaqJJ.png)
試著利用**單調隊列優化**，把複雜度降到$O(N)$
利用單調隊列可以確保所有的數字都只會被push與pop一次，因此總複雜度為$O(n)$
![](https://i.imgur.com/hn2Px6s.png)
差一個K時間就差很多！

具體的作法如下，因為我們要維護的單調隊列的dp後面有一些東西，因此我們要做一些調整，把有i的提出來（不然當i不一樣的時候就很難處理）

$$\begin{split}dp[i] &= val[i]+max(dp[j]-c(i-j)),i-k≤j≤i-1\\&=val[i]-ci+max(dp[j]+cj)\end{split}$$

這時候令 $t[j] = dp[j]+cj$，可以把加上 $cj$ 想像成跟頭的距離（跟當前的 $-ci$ 合在一起就是我們要的距離），則 $max(t[j]),i-k≤j≤i-1$ 就可以使用單調隊列優化！

{% note info %}
**單調隊列優化**

單調隊列優化名稱的由來是因為維護的容器具有單調性（在這裡用到的是單調遞減），這一次嘗試的是用deque<pair<int,int>>來實作，第一維是裝t[i]，第二維是裝i。以下是具體的實作步驟：

1. 將單調隊列中<font color="#f00">過時的</font>（也就是距離大於K的）pop出來，讓容器中的元素都是可以被取到的狀態
2. 更新 $dp[n]$ 的值，利用容器最前面的元素（就是最大的by單調性）來更新。要注意到，元素最前面固然是最大，但也可能會比什麼都不取還差，要特別注意！
3. 從容器的後面更新，把小於接下來要放入的值的元素都pop出來，以維持單調遞減的特性！
4. 將當前的 $t[n]$ 給push進去！結束！

如果要維護dp[n]的單調性，會保證若 $i<j$，則 $dp[i]>dp[j]$；因為如果$dp[i]≤dp[j]$，那第i個永遠不會被取到（因為要求的區間是不斷往後的，若一個數字在前面又比後面的數字小，永遠就不會被取到！）
{% endnote %}

以下是程式碼的部分，其中第21到24行為單調隊列優化的部分：

```cpp=
#include <bits/stdc++.h>
#define int ll
#define ll long long
#define mod 1000000007
#define FOR(i,n) for(int i=0;i<n;i++)
#define ios ios::sync_with_stdio(0)
using namespace std;
int t;

void solve(){
    int n,k,c,val[100005];cin>>n>>k>>c;
    for(int i=1;i<=n;i++)cin>>val[i];
    
    int dp[100005];
    memset(dp,0,sizeof(dp));
    deque<pair<int,int>> deq;
    
    deq.push_back(make_pair(val[1]+c,1));    //這個小細節差點沒有注意到，要加上c
    dp[1] = val[1];
    for(int i=2;i<=n;i++){
        while(!deq.empty() && deq.front().second < i-k)deq.pop_front();
        dp[i] = val[i]-c*i+max(c*i,deq.front().first);
        while(!deq.empty() && deq.back().first <= dp[i]+c*i)deq.pop_back();
        deq.push_back(make_pair(dp[i]+c*i,i));
    }
    int ans = 0;
    for(int i=1;i<=n;i++)ans = max(ans,dp[i]);
    cout<<ans<<endl;
}

signed main(){
    ios;
    cin>>t;
    while(t--){
        solve();
    }
}
```
