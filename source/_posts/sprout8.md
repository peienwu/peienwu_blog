---
title: 資芽第八週：動態規劃（二）
date: 2021-4-24
tags: 
    - 資芽筆記
    - 動態規劃
    - DP
categories: 資訊之芽筆記
mathjax: true
#password: sprout2022
---

這兩週真是風風雨雨，首先第一週是段考週，所以有很多時間拿去惡補段考。第二週則是疫情關係要在家裡線上上課，結果進度就辣了一大截...趕快追QQ！

## 上課內容

一堆的背包問題（真的很多種耶）

**01背包問題**
**無限背包問題**

- 先固定背包數量看價值跟先固定價值看背包數量可以互換
<!--more-->
**有限背包問題**

- 多枚舉每個物品要放入的數量
- 拆分：把同重量的物品看成不同的物品（複雜度不變）
- 把$t_i$ 個物品拆成$\lfloor \log_2 t_i+1\rfloor$個物品
- 分成的堆數最少：$\lceil \log{t_i}+1\rceil$(因為分成 $k$ 堆最多只可能有 $2^k$ 個值)
- 複雜度：$O(n\log{max(t_i)}W)$

**混合背包問題**

- 遇到什麼樣的物品就怎麼做（對有限個背包拆分）
- 分成01背包跟無限背包做

**二維背包問題**

- 多一維度狀態轉移（可以壓掉一個維度）

**分組背包問題**

- 再多一維儲存第幾組

**背包合併**

- 直接把物品混起來做

**背包問題變化**

- (1)求最大價值的方法總數
  - 用g[i] 儲存重量i 的方法數
- (2)求最大價值的一組方案
  - g[i] 看有沒有被更新過，有就g[I] = 1，回溯找
- (3)求最大價值的字典序最小的一組方案
  - 把物品倒過來（由大到小）因為越小的(1)要最後考慮，否則1先考慮後面的大的數字會把1覆蓋掉，字典序就變大
- (4)求次大價值的解/第K大價值的解
  - 看投影片

**分數背包**

- Greedy

**不同做法複雜度**

- 用價值做狀態
- 用重量做狀態
- <font color="#f00">V, W都很大但n很小？
  - 枚舉$2^n$ <font color="#000">
- 如果V, W都很大n也蠻大？
  - 折半枚舉（meet in the middle）根號算法

## 上機作業

背包問題主要有三個變量：價值、重量、物品數量，因此可以有三個作法：

1. 以物品數量n 作為狀態，爆搜，複雜度：$O(2^n)$
2. 以價值v 作為狀態(v為物品價值總和），dp作，複雜度：$O(NV)$
3. 以重量w 作為狀態(w為物品重量上限），dp作，複雜度：$O(NW)$

### 高棕櫚農場

[題目連結](https://neoj.sprout.tw/problem/157/)
這一題不能用重量做，因為重量的範圍可以到$10^5$，因此只能用價值來做
有一個要點，無限大可以memset定義為**0x3f3f3f3f**，以十進位表示1061109567，在int的範圍但不會超過

#### 定義

定義$f(n,m)$為取n樣物品,價值恰為m,重量總和最小值

#### 轉移方式

$f(n,m) = min(f(n-1,m), f(n-1,m-v_n)+w_n), m ≧ v_n$
$f(n,m) = f(n-1,m), m < v_n$

#### 邊界條件

f(0,0) = 0, f(0,k) = INF (k>0)$
因為取零樣物品價值要k不可能達到，因此重量設為無限大

我們可以藉由滾動dp來節省空間，壓成一維（跟用重量作為狀態一樣）
最後，在從dp裡面取出max(k), for all f(N,k) ≦ W

#### 程式碼

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
        int n,m,val[105],weight[100005];cin>>n>>m;
        for(int i=1;i<=n;i++)cin>>weight[i]>>val[i];
        int dp[100005];
        //定義f(n,m)取n樣物品,價值為m,重量總和最小
        //dp[i]：價值為i時,重量最小為dp[i]
        memset(dp,INF,sizeof(dp));
        dp[0] = 0;
        for(int i=1;i<=n;i++){
            for(int j=10000;j>=val[i];j--){
                dp[j] = min(dp[j],dp[j-val[i]]+weight[i]);
            }
        }
        int ans = 0;
        for(int i=0;i<=10000;i++)
            if(dp[i] <= m && i > ans)ans = i;
        cout<<ans<<endl;
    }
}
```

### 高棕櫚農場2

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

### 玩電梯

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

### 取名字好困難QQ

[題目連結](https://neoj.sprout.tw/problem/421/)
跟題目一樣，我覺得要通靈才能想到這一題的作法！
結果是問了別人才大概感受到這一種作法！！！
![](https://i.imgur.com/EmxSHqM.png)

我們既然不知道到底一個數字要不要乘2，我們可以透過做LIS的過程來做決定。當我們把乘與2之後的數字跟原本數字一起push進去，就可以發現到LIS不可能同時取到2個數字。利用這個方法就可以用LIS的過程決定一個數字到底應該要變2倍還是不用。
要找到最長的非嚴格遞增序列，最大的差別就是要把原本的lower_bound改成upper_bound。一整天想一題的感覺超級糟糕QQ

```cpp=
#include <bits/stdc++.h>
#define int long long
#define mod 1000000007
#define FOR(i,n) for(int i=0;i<n;i++)
#define ios ios::sync_with_stdio(0)
using namespace std;
int t;

void solve(){
    int n,m;cin>>n>>m;
    vector<int>vec;
    FOR(i,n){
        int temp;cin>>temp;
        if(2*temp<m)continue;
        else if(temp<m){
            temp*=2;
            vec.push_back(temp);
        }
        else{
            vec.push_back(2*temp);
            vec.push_back(temp);
        }
    }
    if(vec.size()<1){
        cout<<0<<endl;
        return;
    }
    
    //正常做LIS
    vector<int> lis;int len = vec.size();
    lis.push_back(vec[0]);
    for(int i=1;i<len;i++){
        if(lis.back()<=vec[i])lis.push_back(vec[i]);
        else *upper_bound(lis.begin(),lis.end(),vec[i]) = vec[i];
    }
    cout<<lis.size()<<endl;
}

signed main(){
    ios;
    cin>>t;
    while(t--){
        solve();
    }
}

```

## 手寫作業

這一週是講歸約法，作業如下：
![](https://i.imgur.com/amWVYo0.jpg)
這一週手寫作業的狀況還不錯，71/75，不過第一題是緊急向別人求救才把答案改掉
![](https://i.imgur.com/GMtrB99.png)
原本是寫B，後來改A，原因有以下兩點：

1. 因為函數在 return 時不會把 stack memory 上的資料清空，所以所有的區域變數都會被留在 stack memory 上，其他函數可能會共用到同一塊記憶體空間。
2. 有可能會對非法的陣列位址取值(RE的情況)，而存取到別的函數的區域變數。

所以A是錯誤的！
下面是解答的畫法，其實跟我蠻像的XDD
![](https://i.imgur.com/NyN0Kwy.png)
