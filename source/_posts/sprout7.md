---
title: 資芽第七週：動態規劃（一）
date: 2021-4-17
tags: 
    - 資芽筆記
    - 動態規劃
    - DP
categories: 資訊之芽筆記
mathjax: true
---

## 上課內容

- **使用DP時機：**
  - 最優解問題
  - 計數問題
  - 重複子問題

- **DP 使用過程**
  - 定義狀態
  - 狀態轉移
  - 找到一個會AC的作法，再慢慢優化

<!--more-->

- **使用注意事項：**
  - 寫好時間複雜度
  - 用「拉」的
  - 用「推」的
  - 盡量使用由下而上

- **DP例題**
  - 跑步問題（zj b589）
  - 最大連續和
  - 最大不連續和（npsc 2017）
  - Chest of Drawers (UVA 11420)
  - 最大和矩陣問題
  - 矩陣最大方形
  - 矩陣乘法問題
  - 消消樂（UVA 10559 Blocks）

## 上機作業

### 円円數磁磚

[題目連結](https://neoj.sprout.tw/problem/138/)
我不會寫轉移式子啊...
首先可以觀察到，因為兩個磁磚的排法有3種，首先可以列出$f(n) = 3\times f(n-2)$ 這個式子，但觀察之後可以發現，如果排列的磁磚不能被兩兩分割，也可是一種新的排法。而對於固定的尺寸大小只會有一種排法，但因為可以顛倒放，因此視為兩種組合。
**轉移式：**
$f(n) = 3\times f(n-2)+2\times(f(n-4)+f(n-6)...)$
經過數學帶入消去：
$f(n-2) = 3\times f(n-4)+2\times(f(n-6)+f(n-8)...)$
$f(n) = 4\times f(n-2)-f(n-4)$

**邊界：**$dp[0] = 1, dp[2] = 3$

```cpp=
#include <bits/stdc++.h>
#define int unsigned long long
using namespace std;
int t;

signed main(){
    cin>>t;
    int dp[100005];
    memset(dp, 0, sizeof(dp));
    dp[0] = 1;dp[2] = 3;dp[3] = 0;
    for(int i=4;i<=100001;i+=2)dp[i] =(4*dp[i-2]-dp[i-4]+1000007)%1000007;
    while(t--){
        int n;cin>>n;
        cout<<dp[n]<<endl;
    }
}
```

### 円円送禮物

[題目連結](https://neoj.sprout.tw/problem/140/)
以後寫DP幾種組合問題就是像這一題一樣暴力破解
把每一種情況都寫清楚，不惜開三維陣列也沒關係
總之，$dp[i][k][j]$ 代表長度為i，左邊為顏色k，右邊為顏色j
（紅色：0、綠色：1、藍色：2）
列出每一種狀態的轉移式就好
不過，即使頭尾是一藍一綠，但也必須轉移，因為一藍一綠可以在頭或尾加上其他顏色，組成合法解，不過不要輸出就好了

```cpp=
#include <bits/stdc++.h>
#define int long long
#define ios ios::sync_with_stdio(0),cin.tie(0);
using namespace std;
int t;

signed main(){
    ios;
    int dp[100005][3][3];//0:red,1:green,2:blue
    dp[1][0][0] = 1;dp[1][1][1] = 1;dp[1][2][2] = 1;
    dp[2][0][0] = dp[2][0][1] = dp[2][0][2] = 1;
    dp[2][1][0] = dp[2][1][1] = dp[2][2][0] = dp[2][2][2] = 1;
    dp[2][1][2] = dp[2][2][1] = 0;

    for(int i=3;i<100005;i++){
        dp[i][0][0] = (dp[i-1][0][0]+dp[i-1][0][1]+dp[i-1][0][2])%1000007;
        dp[i][0][1] = (dp[i-1][0][0]+dp[i-1][0][1])%1000007;
        dp[i][0][2] = (dp[i-1][0][0]+dp[i-1][0][2])%1000007;
        
        dp[i][1][0] = (dp[i-1][1][0]+dp[i-1][1][1]+dp[i-1][1][2])%1000007;
        dp[i][1][1] = (dp[i-1][1][0]+dp[i-1][1][1])%1000007;
        dp[i][1][2] = (dp[i-1][1][0]+dp[i-1][1][2])%1000007;
        
        dp[i][2][0] = (dp[i-1][2][0]+dp[i-1][2][1]+dp[i-1][2][2])%1000007;
        dp[i][2][1] = (dp[i-1][2][0]+dp[i-1][2][1])%1000007;
        dp[i][2][2] = (dp[i-1][2][0]+dp[i-1][2][2])%1000007;
        
    }
    int n;cin>>n;
    while(n--){
        int a;cin>>a;
        cout<<(dp[a][0][0]+dp[a][0][1]+dp[a][0][2]+dp[a][1][0]+dp[a][1][1]+dp[a][2][0]+dp[a][2][2])%1000007<<endl;
    }
}
```

### 取數字1

[題目連結](https://neoj.sprout.tw/problem/141/)
**定義：**$dp[i]$ 為取第i個數字的的最大值
**轉移式：**$dp[i] = max(dp[i-2]+dp[i-2])+arr[i]$
可以發現到，因為$arr[i]$ 都是正數，因此加越多數字就代表最後的數值越大，而相鄰的兩項不能轉移，因此就要看$dp[i-2],dp[i-2]$其中i-4不用看是因為已經包含在i-2裡面了
**邊界：**$dp[1] = arr[1],dp[2] = arr[2],dp[3] = arr[1]+arr[3]$

```cpp=
#include <bits/stdc++.h>
using namespace std;
int t;

int main(){
    cin>>t;
    while(t--){
        int n,arr[100005];cin>>n;
        int dp[10005];
        memset(dp,0,sizeof(dp));
        memset(arr,0,sizeof(arr));

        for(int i=1;i<=n;i++)cin>>arr[i];
        dp[1] = arr[1];dp[2] = arr[2];dp[3] = arr[1]+arr[3];
        for(int i=4;i<=n+1;i++){
            dp[i] = max(dp[i-2],dp[i-3])+arr[i];
        }
        cout<<max(dp[n],dp[n-1])<<endl;
    }
}
```

### 取數字2

[題目連結](https://neoj.sprout.tw/problem/142/)
上面取數字1的進階版，可以一樣定義，不過轉移式要稍微改變（其實就只是把原本只有2的區間擴大成k）
**定義：**$dp[i]$ 為取第i個數字的的最大值
**轉移式：**
$dp[i]=max(arr[j]+arr[i]),2k\leq i \leq n,i-2k\leq j \leq i-k$
轉移式比較複雜一點，不過就是取數字1的延伸
**邊界：**
$dp[i] = arr[i],1\leq i \leq k$
$dp[i]=max(arr[j]+arr[i]),k+1\leq i \leq 2k,1\leq j \leq i-k$

邊界的話，如果要說成是轉移式也是可以啦

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
        int n,arr[100005],k;cin>>n>>k;
        int dp[100005];
        memset(dp,0,sizeof(dp));
        memset(arr,0,sizeof(arr));

        for(int i=1;i<=n;i++)cin>>arr[i];
        //[1,k]的邊界轉移
        for(int i=1;i<=k;i++)dp[i] = arr[i];
        //[k+1,2k]的邊界轉移
        for(int i=k+1;i<=2*k;i++){
            for(int j=1;j<=i-k;j++){//看可以配上[1,k]最大的那一個
                dp[i]=max(dp[i],arr[j]+arr[i]);
            }
        }
        for(int i=2*k+1;i<=n;i++){
            for(int j = i-2*k;j<=i-k;j++){//開始轉移，掃描k次
                dp[i] = max(dp[i],dp[j]+arr[i]);
            }
        }
        cout<<max_element(dp, dp+n+1)<<endl;
    }
}
```

### 合成円円

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

## 手寫作業

這一週沒有手寫！因為下禮拜是第一階段認證考
