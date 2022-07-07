---
title: "[題解]NEOJ 159 硬幣問題"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 硬幣問題
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/159/)

有點訝異，原本想說把code丟上去測一下，看看能過前面幾筆測資，結果卻直接全部過了！我也沒有做什麼特別的DP優化，丟上去卻直接Accept了！這題跟原本的硬幣問題有些許的不同，在於他有限制每一種硬幣的數量，面額為 $C_i$ 的硬幣共有 $K_i$ 個，也就是要問你能否用這些固定數量的硬幣湊出特定的面額。

我們可以討論不同的面額的餘數，時間複雜度分析估計約為：$O(N\times C_i\times \frac{M}{C_i})=O(NM)$，照理來說因為要執行t次，算下來有可能會TLE，不過這一題沒有卡就是了。

```cpp=
#include <bits/stdc++.h>
#define N 105
#define mod 1000000007
#define FOR(i,n) for(int i=0;i<n;i++)
#define ios ios::sync_with_stdio(0)
using namespace std;
int t;

void solve(){
    int n,m,val[N],num[N];cin>>n>>m;
    for(int i=0;i<n;i++)cin>>val[i]>>num[i];
    
    bool dp[20005];
    memset(dp,0,sizeof(dp));
    dp[0] = 1;
    
    for(int i=0;i<n;i++){
        for(int k=0;k<val[i];k++){
            int left = num[i];
            for(int j=k+val[i];j<=m;j+=val[i]){
                if(dp[j]!=0)left = num[i];
                else if(left>0 && dp[j-val[i]]){
                    left--;
                    dp[j] = 1;
                }
            }
        }
    }
    if(dp[m]==1)cout<<"Yes"<<endl;
    else cout<<"No"<<endl;
}

int main(){
    ios;
    cin>>t;
    while(t--){
        solve();
    }
}
```
