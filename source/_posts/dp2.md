---
title: DP例題
date: 2021-2-1
tags: 
    - C++基礎主題
    - DP
categories:
    - C++基礎主題
    - 動態規劃

mathjax: true
---

## tioj-1354 池塘裡的青蛙

[題序](https://tioj.ck.tp.edu.tw/problems/1354)
這是一個排列組合的數學題目，透過動態規劃紀錄小問題的答案
<!--more-->
### 定義

定義$dp[i][0]$為跳i次不回到A的方法數，$dp[i][1]$為跳i次回到A的方法數

### 轉移方式

* $dp[i][1]$ = $dp[i-1][0]$
應該不難理解，回到A的方法數為上一次不回到A的方法數 * 1
* $dp[i][0] = 3\times dp[i-1][1]+2\times dp[i-1][0]$
不回到A可以分為2種情況，上一次回到A之後，可以往B,C,D，共三種情況。上一次不回到A的情況中，每一個都有兩個可以去的地方。

### 邊界條件

$dp[0][1] = 0, dp[0][0] = 1$

### 程式碼

```cpp=
#include <bits/stdc++.h>
#define int long long
using namespace std;
int t,k;

signed main(){
    cin>>t;
    while(t--){
        cin>>k;
        int dp[k+1][2];
        memset(dp, 0, sizeof(dp));
        dp[0][0] = 0;dp[0][1] = 1;
        for(int i=1;i<=k;i++){
            dp[i][1] = dp[i-1][0];
            dp[i][0] = 3*dp[i-1][1]+2*dp[i-1][0];
        }
        cout<<dp[k][1]<<endl;
    }
}
```

### 複雜度

{% note success %}
這一題很討厭的，沒有給定測資的範圍，不然應該是可以直接建表然後用O(1)的複雜度求答案
{% endnote %}

## tioj-1019 Jumping Up

[題序](https://tioj.ck.tp.edu.tw/problems/1019)

### 定義

定義 dp[i] 為第 0 個到第 i 個鈴鐺的最小移動水平距離總和

### 轉移方式

令$a_0,a_1...a_{n-1}$為相對於螢幕正中央的水平位移，選擇跳一格或跳兩格。

$dp[i] = min(dp[i-1]+abs(a_i-a_{i-1}),dp[i-2]+abs(a_i-a_{i-2}))$

### 邊界條件

$dp[0] = 0,$ $dp[1] = abs(a_1-a_0)$

### 程式碼

```cpp=
#include <bits/stdc++.h>
#define int long long
using namespace std;
int T,N;

signed main(){
    cin>>T;
    while(T--){
        cin>>N;
        int arr[N+1],dp[N+1];
        memset(arr, 0, sizeof(arr));
        memset(dp, 0, sizeof(dp));
        
        for(int i=0;i<N;i++)cin>>arr[i];
        dp[0] = 0;dp[1] = abs(arr[1]-arr[0]);
        
        for(int i=2;i<N;i++){
            dp[i] = min(dp[i-1]+abs(arr[i]-arr[i-1]),
                        dp[i-2]+abs(arr[i]-arr[i-2]));
        }
        cout<<dp[N-1]<<endl;
    }
}
```

### 複雜度

{% note info %}
這一題$dp_i$只會跟前兩項有關係，好像可以用**滾動DP**來優化空間，可以以後嘗試看看！
{% endnote %}

## tioj-1097 . F.營地

[tioj1097題序](https://tioj.ck.tp.edu.tw/problems/1097)
[leetcode題序](https://leetcode.com/problems/maximal-square/)

### 定義

定義 **dp[i][j]** 為以索引(i,j)方格為正方形右下角所形成的最大正方形邊長

### 轉移方式

$dp(i,j)=min(dp(i−1,j),dp(i−1,j−1),dp(i,j−1))+1$
看一個格字是否可以構成更大的正方形，要以它上方、左方、左上方的格子來決定（左、左上、上跟它自己剛好可以構成一個正方形）

### 邊界條件

初始化為題目給定的0與1（0代表不能放、1表示可以放）

### 程式碼

leetcode 221

```cpp=
class Solution {
public:
    int maximalSquare(vector<vector<char>>& matrix) {
        int row = matrix.size(),col = matrix[0].size(),ans = 0;;
        
        for(int i=0;i<row;i++){
            for(int j=0;j<col;j++){
                matrix[i][j]-='0';
                if(matrix[i][j]==1&& ans ==0)ans = 1;
                if(i==0||j==0)continue;
                
                if(matrix[i][j]==1){
                    matrix[i][j] = min(min(matrix[i-1][j],matrix[i][j-1]),
                                       matrix[i-1][j-1])+1;
                    ans = max((int)matrix[i][j],ans);
                }
            }  
        }
        return ans*ans;
    }
};
```

tioj 1097

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define int long long
using namespace std;
int L,W,dp[2][5005],ans;

void input(int ind){
    for(int k=0;k<W;k++){
            int temp;
            cin>>temp;
            if(temp == 2)dp[ind][k] = 0;
            else dp[ind][k] = 1;
            if(dp[ind][k]==1&&ans == 0)ans = 1;
    }
}

signed main(){
    ios;
    while(cin>>L>>W){
        ans = 0;
        if(L==0 && W==0)break;
        input(0);
        
        for(int i=0;i<L-1;i++){
            input(1);
            for(int j=1;j<W;j++){
                if(dp[1][j]==1){
                    dp[1][j] = min(dp[0][j],min(dp[0][j-1],dp[1][j-1]))+1;
                    ans = max(ans,dp[1][j]);
                }
            }
            for(int i=0;i<W;i++)dp[0][i] = dp[1][i];
        }
        cout<<ans*ans<<endl;
    }
}
```

### 複雜度

{% note info %}
實作遇到的一些問題：

1. 陣列直接開空間複雜度會爆掉，所以要用到**滾動DP** （不然會MLE）
2. 當i=0 與j=0 時會被忽略掉，如果只有在i=0 或j=0 出現1而其他都是0（不能放）的時候，就會出現答案為0的情況，在輸入時就要做修正
3. 因為使用了滾動DP，所以在一開始會先輸入第一排，這時總共剩下L-1排，所以在第24行要是L-1次，才不會需要多輸入一行。
{% endnote %}

## 1029  A遊戲

[tioj1029](https://tioj.ck.tp.edu.tw/problems/1029)
> 有一串由N個正整數所組成的數列，兩個玩者輪流拿走一個最左邊或最右邊的數，直到最後所有的數都取完之後，兩個玩者分別把自己所取到數加總，分數較高的人獲勝。
> 範例輸入
> 6
4 7 2 9 5 2
輸出 18 11

### 定義

這一題用到的是[區間DP](https://https://www.csie.ntu.edu.tw/~sprout/algo2018/ppt_pdf/dynamic_programming_2_inclass.pdf)
定義dp[i][j]為區間$[i,j]$中**先手可以拿到的最大值**，因為先後手拿的總和不變，所以**後手**在區間$[i,j]$ 可以拿到的最大值為 $sum[i,j]-dp[i][j]$。
同時，$dp[i][i] = arr[i]$    (先手取)

### 轉移方式

由題目可知，先後手都是以最佳策略來玩這個遊戲，而區間$[l,r]$中先手在區間$[l+1,r]$ 中就變成後手（對手先拿），因此可以推得轉移式：
$$dp[i][j] = max(sum[i+1,j]-dp[i+1][j]+arr[i],
                sum[i,j-1]-dp[i][j-1]+arr[j])$$
（第一項取最左邊，第二項代表取最右邊）
![](https://i.imgur.com/3YlunPi.png)
以此圖為例，以i為橫軸代表出發點，j為縱軸代表終點，由上而下、由右而左依序把左下角的表格填滿。所求即為(i,j) = (1,6)。

### 邊界條件

$dp[i][i] = arr[i]$

### 程式碼

上方的sum，可以透過表格儲存**前綴和**，快速求得

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0);cin.tie(0);
using namespace std;
int n;

int main(){
    ios
    cin>>n;
    int dp[n+1][n+1],pref[n+1],arr[n+1];
    memset(dp,0,sizeof(dp));
    pref[0] = 0;
    for(int i=1;i<=n;i++){
        cin>>arr[i];
        dp[i][i] = arr[i];
        pref[i] = pref[i-1]+arr[i];
    }
    for(int i=n-1;i>0;i--){
        for(int j=i+1;j<=n;j++){
            dp[i][j] = max(pref[j]-pref[i]-dp[i+1][j]+arr[i],//右格
                           pref[j-1]-pref[i-1]-dp[i][j-1]+arr[j]);//上格
        }
    }
    cout<<dp[1][n]<<" "<<pref[n]-dp[1][n]<<endl;
    
//    for(int j=1;j<n+1;j++){
//        for(int i=1;i<=n;i++)cout<<dp[i][j]<<" ";
//        cout<<endl;//印出dp
//    }
}
```

{% note success %}
[Leetcode 877](https://leetcode.com/problemset/all/?search=stone%20game)這裡有一系列的stone game 變化題

```cpp=
class Solution {
public:
    bool stoneGame(vector<int>& piles) {
        int n = piles.size();
        piles.insert(piles.begin(),0);
        int dp[n+1][n+1],pref[n+1];
        memset(dp,0,sizeof(dp));
        memset(pref,0,sizeof(pref));
        for(int i=1;i<=n;i++){
            dp[i][i] = piles[i];
            pref[i] = pref[i-1]+piles[i];
        }
        
        for(int i=n-1;i>0;i--){
            for(int j=i+1;j<=n;j++){
                dp[i][j] = max(pref[j]-pref[i]-dp[i+1][j]+piles[i],
                              pref[j-1]-pref[i-1]-dp[i][j-1]+piles[j]);
            }
        }
        cout<<dp[1][n]<<endl;
        if(dp[1][n]>pref[n]/2)return true;
        else return false;
    }
};

```

這一題有一個數學解，可以保證答案一定是true。

簡單來說，alex可以控制對方拿到的一定是白色堆或是黑色堆，因此只要在一開始選擇數量較大的顏色就保證可以贏得遊戲
{% endnote %}

### 複雜度

$O(N^2)$
{% note success %}
這一題除了轉移式比較難想之外，要怎麼樣轉移也是一個問題。轉移方式可以透過小範圍測資試著找轉移的規律，如何轉移則可以透過畫表格的方式理解。
{% endnote %}
