---
title: "[題解]TIOJ 1096 E.漢米頓的麻煩"
date: 2021-1-7
tags: 
    - 最短路徑
    - 題解
categories: 最短路徑題解
mathjax: true
---

### E.漢米頓的麻煩
<!-- more -->
[題目連結](https://tioj.ck.tp.edu.tw/problems/1096)

他題目時不時提到漢米頓，是多愛漢米頓XDD（這一題跟漢米頓根本沒關係）

> 題目大意
> 在一張n(n≤100)個點的圖中，尋找路徑長度最短的環

那就用**Floyd-Warshall**找最小的環就好！

{% note info %}
**Floyd-Warshall**
這就是dp的作法，用 $O(N^3)$ 的時間進行轉移，就能得到全點對的最短路徑。這邊有一個重要的轉移順序，也就是中點-起點-終點進行轉移，如果把dp展開就會發現中點必須在最外層進行轉移：

> 定義 $dp[k][i][j]$ 為點 $i$ 走到點 $j$ ，只能經過前k個點的最短路徑，則轉移式變成：
> $$d[k+1][i][j] = min(d[k][i][j], d[k][i][k+1]+d[k][k+1][j])$$

每一個k+1都是由k轉移而來，仰賴k的點的配對，因此必須最先轉移中點k的部分！（不過順序顛倒也不會怎樣啦，上面「課程內容」的地方有寫到）
{% endnote %}
同樣這一份code也可以判斷有沒有負環，只要ans小於0的話代表有負環（有的話距離是不能用的喔，因為Floyd-Warshall不能處理負環，但負邊是可以處理的）

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define N 101
#define FOR(i,n) for(int i=0;i<n;i++)
#define pii pair<int,int>
#define pid pair<int,double>
#define pdi pair<double,int>
using namespace std;
int n,dp[N][N];

void solve(){
    memset(dp,0x3f3f3f3f,sizeof(dp));
    for(int i=0;i<n;i++){
        for(int j=0;j<n;j++){
            int temp;cin>>temp;
            if(temp!=0)dp[i][j] = temp;
        }
    }
    for(int k=0;k<n;k++){
        for(int i=0;i<n;i++){
            for(int j=0;j<n;j++){
                dp[i][j] = min(dp[i][j],dp[i][k]+dp[k][j]);
            }
        }
    }
    int ans = INT_MAX;
    for(int i=0;i<n;i++){
        ans = min(ans,dp[i][i]);
    }
    if(ans == INT_MAX)cout<<-1<<endl;
    else cout<<ans<<endl;
}

signed main(){
    while(cin>>n && n!=0){
        solve();
    }
}
```
