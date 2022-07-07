---
title: "[題解]NEOJ 158 高棕櫚農場2"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 高棕櫚農場2
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/158/)
有些細節是必須要注意的，也就是初始化的細節。
{% note success %}
**背包問題是否恰好裝滿**

對於原本初始化dp[0] = 0，代表對於重量限制為0的背包價值最高為0
接下來有兩種情況需要討論，第一種是重量限制為w的背包最多的價值

**1. 恰好裝滿**
此時必須初始化dp[i] = -INF，是因為要恰好裝滿的關係，初始化的dp 數組事實上就是**在沒有任何物品可以放入背包時的合法狀態**，其他除了0之外容量的背包均沒有合法的解，屬於未定義的狀態，所以都應該被賦值為 −∞ 。當前的合法解，一定是從之前的合法狀態推得的(−∞跟−∞取max還是−∞)

**2. 不需恰好裝滿**
如果背包並非必須被裝滿，那麼任何容量的背包都有一個合法解“什麼也不裝”，這個解的價值為0,所以初始化時狀態的值也就全部為0了。

如果來看轉移式，$dp[j] = max(dp[j],dp[j-weight[i]]+val[i])$，如果兩者的狀態都屬於未定義，對於需恰好裝滿的狀況，兩者都是−∞，表示沒有合法的狀態可以構成此重量。同時，如果不需恰好裝滿的情況，即使$dp[j]$和$dp[j-weight[i]]$都未定義(等於0)，還是可以被更新（在沒有裝滿的情況下，dp[j] = val[i]）
{% endnote %}
這一題除了以上發現，還有一個很重要的東西，就是迴圈到底要放哪一層的問題。主要是卡在 for(int p=1;p<=k;p++)到底要放在哪一層的問題，結果是要放在第三層。
{% note info %}
問題一：dp[j][p]取決於dp[j][p] 和dp[j-weight[i]][p-1]，而且對於一個物品最多只能放一次，如果放在第二層，dp[j-weight[i]][p-1] 就已經被更新過了，有可能已經取了第 i 樣物品會有重複取的問題，如果放在第三層，代表對每一種不同的重量先更新放入幾樣物品的1到k，再更新重量，這樣就可以保證dp[j][p]不會取到已經更新的格子（dp[j][p] 沒被更新、dp[j-weight[i]][p-1] 其中第一維的j-weight[i] 也還沒被更新）

問題二：p要從1到k還是k到1，這其實都可以，因為要取的格子不管從前往後或後往前取都只會取到上一輪(i-1) 的更新東西，因此不影響。還有，因為是定義**最多取p樣物品**，所以無論i為多少，每一次p皆要更新的k（如果k=5，取一樣物品也符合情況）
{% endnote %}

#### 定義

定義$f(j,p)$看完 *i* 樣物品後，重量限制為j，**最多**取p樣物品的最大價值

#### 轉移方式

$dp[j][p] = max(dp[j][p],dp[j-weight[i]][p-1]+val[i])$

#### 邊界條件

$dp[i][j] = 0$ (for all elements in dp)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define INF 0x3f3f3f3f
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int t;

signed main(){
    ios;
    cin>>t;
    while(t--){
        int n,m,k,val[105],weight[10005];cin>>n>>m>>k;
        for(int i=1;i<=n;i++)cin>>weight[i]>>val[i];
        int dp[1005][105];
        memset(dp, 0, sizeof(dp));
        for(int i=1;i<=n;i++){
            for(int j=m;j>=weight[i];j--){
                for(int p=1;p<=k;p++){
                    dp[j][p] = max(dp[j][p],dp[j-weight[i]][p-1]+val[i]);
                }
            }
        }
        cout<<dp[m][k]<<endl;
    }
}
/*
 0 4 5 6 6 6
 0 4 5 6 9 10
 0 4 5 6 9 10
*/
```
