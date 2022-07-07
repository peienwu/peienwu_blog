---
title: "[題解]NEOJ 255 憤怒的小鳥"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 憤怒的小鳥
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/255/)
[Submission](https://neoj.sprout.tw/challenge/177536/)
玩憤怒鳥遊戲的概念，給定二維平面上n個點代表n個豬（皆在第一象限），這時候可以從原點(0,0)的位置發射軌跡為拋物線的小鳥，打到豬豬後並不會改變飛行的軌跡，題目想要問最少要發射幾個小鳥才能射下所有的豬豬？

> 範例測資
> 7
> 1 3
> 2 4
> 3 3
> 2 6
> 4 8
> 1 1
> 1 2

如果畫在圖上就長這樣：
![](https://i.imgur.com/cCEHouc.png)

如果把拋物線畫出來就像這樣：
![](https://i.imgur.com/u63WTC6.png)

因為$n\le24$，所以可以知道這一題可能只能把所有情況都掃一遍（~~才不是因為剛好講狀態壓縮XD~~）

**作法1：** 時間複雜度$O(n^2\cdot 2^n)$
我們可以利用一個二維陣列g[i][j]表示經過i豬和j豬與原點所形成的拋物線所能經過的所有點的狀態。這樣我們可以利用$O(n^3)$的時間完成預處理，知道一條由i豬和j豬形成的拋物線能經過的點的狀態（用二進位表示），在dp轉移的時候則需枚舉所有的i和j，所需時間是$n^2$。
轉移式的話一開始是寫用推的：$dp[s|g[i][j]] = min(dp[s]+1)$，比較直覺一點。

**作法2：** 時間複雜度$O(n\cdot 2^n)$
當我們在做狀態s的時候，如果是上面的方法就是$n^2$掃過所有的點的組合，其實我們可以只要找到s其中一位第k位是1的，因為那一位所對應到的豬豬一定會經過，所以就可以針對那一位做轉移，如此一來跟k同在一個拋物線的最多只會有n個，複雜度就降到了$O(n\cdot 2^n)$。這裡可以搭配一個vector<int>line[N]裝每一位有經過的可能的狀態。

#### 定義

$dp[s]$為狀態為s的時候的最小拋物線數量

#### 轉移式

$dp[s]=min(dp[s\&(\sim mask)]+1)$

這個轉移式挺有趣的，我們知道如果要從狀態p得到狀態s，對於s是0的bit，p也應該是0；對於s是1的bit，若mask是1，則p的bit要是0，反之則是1。

```flow
st=>start: for each bit p in s
sub1=>subroutine: My Subroutine
cond=>condition: if p=1
e=>operation: set dp[i] to 0
io=>condition: if mask[i]=1
ss=>operation: set dp[i] to 1
sp1=>operation: set dp[i] to 0
sp2=>operation: set dp[i] to 1

st->cond->io
cond(yes)->io->e
cond(no)->e

io(yes)->sp1
io(no)->sp2
```

這個轉移式可以有另外一個表示的方式，可以達到同樣的效果：
$dp[s]=min(dp[s\&(i\oplus mask)]+1)$

#### 邊界

$for\ each\ dp[i]=0$

以下程式碼是以$O(n^3)$的預處理，$O(n\cdot 2^n)$的轉移，因此總時間複雜度是$O(2^n)$，在時限內是可以過的！
![](https://i.imgur.com/j8uRDfj.png)

> [time=Thu, Sep 16, 2021 3:03 PM]更新

結果呢，有人問了我這一題，我回去看我的code，結果發現明明應該是 $O(n\cdot 2^n)$ 轉移，但每一個line_mask中在worst case卻有 $n^2$ 個狀態，這樣子下來一樣還是 $O(n^2\cdot 2^n)$ 沒有進步。發現問題在於，當全部的點都在同一條拋物線上的時候，會有重複的狀態被push很多次的問題，所以只要用visit去紀錄這種狀態是否出現過，讓最多被推入一次就好!

![](https://i.imgur.com/V2ubK6i.png)

```cpp=
#include <bits/stdc++.h>
#define long long ll
#define N 24
#define mod 1000000007
#define FOR(i,n) for(int i=0;i<n;i++)
using namespace std;
int t,n,x[N],y[N];

void solve(){
    vector<int>line_mask[N];
    scanf("%d",&n);
    for(int i = 0;i < n;i++)scanf("%d %d",&x[i],&y[i]);
    int m = 1<<n,dp[m];
    bool visit[m];memset(visit,0,sizeof(visit));
    //calculate all lines O(n^3)
    for(int i=0;i<n;i++){
        line_mask[i].push_back(1<<i);       //有可能此點孤立一人，要確保也在裡面
        for(int j=i+1;j<n;j++){
            int x1 = x[i],y1 = y[i];
            int x2 = x[j],y2 = y[j];
            if(x1==x2)continue;

            double a = (double)((x1*y2)-(x2*y1))/(x1*x2*(x2-x1));
            double b = (double)(y1-a*x1*x1)/x1;
            if(a >= 0)continue;

            int mask1 = 1<<i|1<<j;
            for(int k = 0;k < n;k++){
                if(fabs((double)a*x[k]*x[k]+b*x[k]-y[k])<=1e-6)mask1|=(1<<k);
            }
            if(visit[mask1])continue;
            for(int k = 0;k < n;k++){
                if((mask1>>k) & 1)line_mask[k].push_back(mask1);
            }
            visit[mask1] = 1;
            //過k點的所有經過的點的狀態
        }
    }
    memset(dp,0x3f3f3f,sizeof(dp));
    dp[0] = 0;

    //O(n 2^n轉移)
    for(int s = 1;s < m;s++){
        int cur = __lg(s),len = line_mask[cur].size();
        for(int i = 0;i < len;i++){
            int mk = line_mask[cur][i];
            dp[s] = min(dp[s], dp[(s&(~mk))] + 1);
            if(dp[s] == 1)break;
        }
    }
    printf("%d\n",dp[m-1]);
}

int main(){
    scanf("%d",&t);
    while(t--){
        solve();
    }
}
```
