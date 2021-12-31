---
title: DP問題-經典題學習
date: 2021-1-30
tags: 
    - 演算法
    - 動態規劃
categories:
    - C++基礎主題
    - 動態規劃

mathjax: true
---
## 動態規劃的要素
### 可以動態規劃的問題符合兩個條件：
* 重複子問題
* 可分治性
### 動態規劃的三要素
* 0.定義
* 1.狀態轉移方式
* 2.初始狀態（邊界條件）

<!--more-->

### 動態規劃應注意的問題
1. 轉移方式：Top-Down or Bottom-up
2. 複雜度=狀態數*轉移時間
3. 轉移順序


## [IOI 1994] 三角旅行

![](https://i.imgur.com/O8lT7c9.png)

### 0.定義
定義陣列dp[i][j]為走到第 i 橫排第 j 格的時候可能的最大值。
### 1.轉移方式
第 i 橫排第 j 格的解為：「第 i+1 橫排第 j 格」與「第 i+1 橫排第 j+1 格」的最大值，加上原本第 i 橫排第 j 格的值。
以範測為例:
![](https://i.imgur.com/JJtfbys.jpg)
轉移程式碼：
```cpp=
dp[i][j] = max(dp[i+1][j],dp[i+1][j+1])+arr[i][j];
```
### 2.邊界條件
我是從第 n-1 層開始dp轉移，所以邊界就是第 n 層的所有元素
```cpp=
for(int i=0;i<n;i++)
    dp[n-1][i] = arr[n-1][i];
```
而答案即為**dp[0][0]**，也就是三角形的頂端
### 程式碼
```cpp=
#include <iostream>
#include <algorithm>
using namespace std;
int n,dp[105][105] = {0},arr[105][105] = {0};

int main(){
    cin>>n;
    for(int i=0;i<n;i++)
        for(int j=0;j<=i;j++)cin>>arr[i][j];
    
    for(int i=0;i<n;i++)dp[n-1][i] = arr[n-1][i];
    
    for(int i=n-2;i>=0;i--){
        for(int j=0;j<=i;j++){
            dp[i][j] = max(dp[i+1][j],dp[i+1][j+1])+arr[i][j];
        }
    }
    cout<<dp[0][0]<<endl;
}
```
## 最大子陣列
[leetcode 53](https://leetcode.com/problems/maximum-subarray/)
暴力解（**O(n^2^)**）或是用動態規劃處理（**O(n)**）
方法：利用變數 ***sum*** 加總a[0],a[1] 到 a[n-1]，在加總過程中，如果出現負數，則代表對最大子陣列沒有幫助（只會越來越少，所以捨棄），則讓sum=0 ，繼續加總，並在每一次相加過程紀錄最大值 ***max***。
```cpp=
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int sum = 0,len = nums.size(),max = -1e9;
        for(int i=0;i<len;i++){
            sum+=nums[i];
            if(sum>max)max = sum;
            if(sum<0)sum = 0;
        }
        return max;
    }
};
```
## 背包問題
背包問題總共有以下幾種：
1. **分數背包**（一個物品可以切下一部分、只取幾分之幾放進背包）—*Greedy*
2. **01背包問題**
3. **無限背包**

### 分數背包
策略：貪心法。每一次取單位價值最高的物品，優先放進背包。
總是用當下最好的物品填滿背包空隙，最後沒有留下任何空隙。每一份背包空間，都是最有價值的物品，就算是交換物品也無法增加總價值 ── 顯然是最佳解。
時間複雜度：$O(n)$，n為物品數量

### 01背包問題
####  不考慮最佳解路徑
**解一**：暴力法（遞迴求解）。枚舉每一種物品要放或不放進背包，但複雜度會爆掉 $O(2^N)$
以zerojudge b184 為例：
```cpp=
#include <bits/stdc++.h>
using namespace std;
int weight[105],val[60005];

int back_pack(int n,int w){//回傳n樣物品耐重為w的最大價值
    if(w<0)return -1e9;//耐重為負，只能不取
    if(n==0)return 0;
    
    return max(
       back_pack(n-1,w-weight[n])+val[n],//取第n個物品
       back_pack(n-1,w));//不取第n個物品
}
int main(){
    int n;
    while(cin>>n){
        for(int i=1;i<=n;i++)cin>>weight[i]>>val[i];
        cout<<back_pack(n,100)<<endl;
    }
}
```
{% note warning %}
```cpp=
if(w<0)return -1e9;
if(n==0)return 0;
```
這兩行程式碼**不能**顛倒放，否則在最末端的計算上會出現問題！
（在這邊debug 超久，顛倒放會讓不該放的物品有可能被放進背包 :zany_face:）
{% endnote %}

**解二**：因為遞迴的關係，可以透過二維陣列紀錄小問題的解，這樣可以更有效率
```cpp=
#include <bits/stdc++.h>
using namespace std;
int weight[105],val[60005];
int dp[1000][105] = {0};//dp[][]儲存放n樣物品耐重w的最大利潤

int back_pack(int n,int w){//回傳n樣物品耐重為w的最大價值
    if(w<0)return -1e9;
    if(n==0)return 0;
    if(dp[n][w])return dp[n][w];
    
    return dp[n][w] = max(
       back_pack(n-1,w-weight[n])+val[n],
       back_pack(n-1,w));
}
int main(){
    int n;
    while(cin>>n){
        for(int i=1;i<=n;i++)cin>>weight[i]>>val[i];
        cout<<back_pack(n,100)<<endl;
        for(int i=0;i<1000;i++)for(int k=0;k<105;k++)dp[i][k] = 0;
    }
}
```
**解三**：可以重複利用記憶體，建立一條陣列；不過計算順序要改成由陣列後端開始– 從前端可能會重複使用到同一個物品，而**物品只有一個，不能重複利用**(可以重複利用就變成無限背包)。
```cpp=
#include <bits/stdc++.h>
using namespace std;
int weight[105],val[60005];
int main(){
    int n,w;
    cin>>n;
    for(int i=0;i<n;i++)cin>>weight[i]>>val[i];
    cin>>w;
    
    int dp[105] = {0};
    for(int i=0;i<n;i++){
        for(int j=w;j>=weight[i];j--){
            dp[j] = max(dp[j],dp[j-weight[i]]+val[i]);
        }
    }
    cout<<dp[w]<<endl;
}
```
時間複雜度 ***O(NW)*** ，空間複雜度 ***O(W)*** 。其中 N 是物品數量， W 是背包重量限制。（上方11-15行）

**解四**：用價值作為狀態，dp紀錄重量的最小值，可以看[這裡](https://hackmd.io/P28wmXFHQ_-yZuEFQnfVuA)
```cpp=
#include <bits/stdc++.h>
#define INF 0x3f3f3f3f
using namespace std;
int t;

signed main(){
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
時間複雜度 ***O(NV)*** ，空間複雜度 ***O(V)*** 。其中 N 是物品數量， V是價值總和。（上方11-15行）

細節注意：
{% note success %}
**背包問題是否恰好裝滿**

對於原本初始化dp[0] = 0，代表對於重量限制為0的背包價值最高為0
接下來有兩種情況需要討論，第一種是重量限制為w的背包最多的價值

**1. 恰好裝滿**
此時必須初始化dp[i] = -INF，是因為要恰好裝滿的關係，初始化的dp 數組事實上就是**在沒有任何物品可以放入背包時的合法狀態**，其他除了0之外容量的背包均沒有合法的解，屬於未定義的狀態，所以都應該被賦值為 −∞ 。當前的合法解，一定是從之前的合法狀態推得的

**2. 不需恰好裝滿**
如果背包並非必須被裝滿，那麼任何容量的背包都有一個合法解“什麼也不裝”，這個解的價值為0,所以初始化時狀態的值也就全部為0了。
{% endnote %}

#### 考慮最佳解路徑
需要二維陣列 *p[i][j]* 紀錄第 i 個物品在耐重 j 下是否有放入背包
由下面的程式碼可以發現，一個物品如果放入背包，則更新p[i][j]為1
```cpp=
#include <bits/stdc++.h>
using namespace std;
int weight[105],val[60005],p[105][105] = {0};
int main(){
    int n,w;
    cin>>n;
    for(int i=0;i<n;i++)cin>>weight[i]>>val[i];
    cin>>w;
    
    int dp[105] = {0};
    for(int i=0;i<n;i++){
        for(int j=w;j>=weight[i];j--){
            if(dp[j-weight[i]]+val[i]>dp[j]){
                dp[j] = dp[j-weight[i]]+val[i];
                p[i][j] = 1;
            }
        }
    }
    cout<<dp[w]<<endl;
    for(int i=n-1,j = w;i>=0;i--){
        if(p[i][j]){
            cout<<"第"<<i+1<<"個物品放入背包"<<endl;
            j-=weight[i];
        }
    }
}
```
為什麼上方第20行程式碼要從n-1開始跑？
{% note default %}
以下測資：
> 4
> 3 20
> 4 45
> 9 70
> 12 85
> 12

可以發現第1,3物品放入背包，但如果變成以下測資：
> 5
3 20
4 45
9 70
12 85
3 100
12

第五個物品可以取代第一個物品，如果從 j=0 開始計算，會把第一個物品也算進去，而這樣是錯的。從 n-1 開始計算可以確保答案正確。

其實從後面計算才能算到在w耐重下最後一個被加入背包的物品，如果從前面開始計算，前面不是最佳解的物品會被算進去。
![](https://i.imgur.com/j93svqK.png)
以這個p[][]的結果來說，最後一個被加進去的才是最佳解，前面都是被替換掉的（在陣列dp[]中被取代，因為**陣列dp[]被更新的條件就是有價值更高的解**）
{% endnote %}

轉移方向的不同：
{% note info %}
01背包問題在壓成一維的過程中必須知道是怎麼來的，從二維壓成一維，dp更新的順序要從前到後或後到前要看是存取到哪一格的內容。
這是**01背包問題**轉移式所用到的格子：
$f(n,m) = max(f(n-1,m),f(n-1,m-w_n)+v_n)$
![](https://i.imgur.com/zavoNSH.png)
對於每一個f(n,m)只會取用到2個格子$f(n-1,m)$和$f(n-1,m-w_n)$兩個格子，如果從前到後更新格子的話，左上方的藍色格子會先被更新到，所以改成一維陣列之後必須要從後往前更新
接下來是**無限背包問題**：
![](https://i.imgur.com/Io9scMn.png)
左邊的藍色格子是被更新後的，因此無限背包問題必須從前到後更新
{% endnote %}

### 無限背包
物品有許多種類，每一種物品都無限量供應的背包問題。
演算法：跟解三差不多，因為物品有**無限多個**，所以第二個迴圈要從 *weight[i]* 開始執行：
```cpp=
#include <bits/stdc++.h>
using namespace std;
int weight[105],val[60005];
int main(){
    int n,w;
    cin>>n;
    for(int i=0;i<n;i++)cin>>weight[i]>>val[i];
    cin>>w;
    
    int dp[105] = {0};
    for(int i=0;i<n;i++){
        for(int j=weight[i];j<=w;j++){
            dp[j] = max(dp[j],dp[j-weight[i]]+val[i]);
        }
    }
    cout<<dp[w]<<endl;
}
```
## 找零錢問題
找零錢問題總共有以下幾種：
1. 湊得某價位的最少錢幣用量
2. 湊得某個價位的湊法總共幾種

找零錢問題就是弱化版的背包問題！

### 湊得某價位的最少錢幣用量
#### 不考慮最佳解路徑
定義：**dp(n,m)** 代表用第0種到第n種錢幣湊得價位m的最少硬幣數
初始值：dp值設為 $\infty$（求最少），dp[0] = 0
轉移方式：$dp[j] = min(dp[j], dp[j-val[i]]+1)$

```cpp=
#include <bits/stdc++.h>
using namespace std;
int n,dp[105],val[100],target;

int main(){
    cin>>n;
    for(int i=0;i<n;i++)cin>>val[i];
    cin>>target;
    memset(dp, 0x6f, sizeof(dp));
    dp[0] = 0;
    for(int i=0;i<n;i++){
        for(int j=val[i];j<=target;j++){
            dp[j] = min(dp[j], dp[j-val[i]]+1);
        }
    }
    cout<<dp[target]<<endl;
}
```

####  考慮最佳解路徑
使用陣列p[i] 代表**達成金額i元最後加入的硬幣面額（即達成最小硬幣數量的面額）**
```cpp=
#include <bits/stdc++.h>
using namespace std;
int n,dp[105],val[100],target,p[105];
//p[i]達成金額i元最後加入的硬幣面額

int main(){
    cin>>n;
    for(int i=0;i<n;i++)cin>>val[i];
    cin>>target;
    memset(dp, 0x6f, sizeof(dp));
    memset(p, 0, sizeof(p));
    dp[0] = 0;
    for(int i=0;i<n;i++){
        for(int j=val[i];j<=target;j++){
            if(dp[j]>dp[j-val[i]]+1){//加入第i種硬幣，使數量更少
                dp[j] = dp[j-val[i]]+1;
                p[j] = val[i];
            }
        }
    }//陣列p留下來的即為達成金額i元最後加入的硬幣面額
    cout<<dp[target]<<endl;
    int  k= target;
    while(k>0){
        cout<<p[k]<<" ";
        k = k - p[k];//前往下一個錢幣數目
    }
    cout<<endl;
}

```
### 湊得某個價位的湊法總共幾種
定義：**dp(n,m)** 代表用第0種到第n種錢幣湊得價位m的湊法數目
初始值：dp[0]=1(雖然現實中不可能湊到0元)
轉移方式：$dp[j] =dp[j] + dp[j-val[i]]$
{% note info %}
程式碼中使用 **+=** 是因為「不加上第n種錢幣的湊法（就是未更新的dp[j]）」加上「加上第n種錢幣的湊法（剩下的就是湊滿 **j-val[i]** 的湊法）」
{% endnote %}
```cpp=
#include <bits/stdc++.h>
using namespace std;
int n,dp[1005],val[105],target;

int main(){
    cin>>n;
    for(int i=0;i<n;i++)cin>>val[i];
    cin>>target;
    memset(dp, 0, sizeof(dp));
    dp[0] = 1;
    for(int i=0;i<n;i++){
        for(int j=val[i];j<=target;j++){
            dp[j] += dp[j-val[i]];
        }
    }
    cout<<dp[target]<<endl;
}
```
## 最長上升子序列（LIS）
> 給你一個正整數序列，問你最多可以從裡面選出幾個元素（不改變順序），使得這些元素嚴格遞增。

###  不考慮最佳解路徑
解一：複雜度（O(n^2^)）
定義：以陣列dp[i] 代表長度為序列長度為 i 時的LIS(讓dp[i]表示取了a[i]為最後一個數字的LIS)
初始值：dp中的每一個元素初始值為1
轉移方式：將第 i 個元素設為第 0 到 i-1 的元素之最大值加一：
$dp[i] = max_{j<i,a[j]<a[i]} (dp[j])+1$

以[tioj 1175 Longest Increasing Subsequence](https://tioj.ck.tp.edu.tw/problems/1175)為例：
```cpp=
#include <bits/stdc++.h>
using namespace std;
int n,arr[100005],dp[100005];

int main(){
    cin>>n;
    memset(arr, 0, sizeof(arr));
    memset(dp, 0, sizeof(dp));
    for(int i=0;i<n;i++){
        cin>>arr[i];
        dp[i] = 1;
    }
    for(int i=1;i<n;i++){
        for(int j=0;j<i;j++){
            if(arr[j]<arr[i])
                dp[i] = max(dp[i],dp[j]+1);
        }
    }
    int ans = *max_element(dp,dp+n);
    cout<<ans<<endl;
}
```

解二：複雜度（$O(nlogn)$）
可以利用*vector*維護一個嚴格遞增的序列，同時利用lower_bound 來二分搜a[i]該在序列中的哪一個位置
以 [leetcode 300](https://leetcode.com/problems/longest-increasing-subsequence/)為例：

```cpp=
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        vector<int> lis;
        int len=nums.size();
        lis.push_back(nums[0]);
        for(int i=1;i<len;i++){
            if(lis.back()<nums[i])lis.push_back(nums[i]);
            else *lower_bound(lis.begin(),lis.end(),nums[i]) = nums[i];
        }
        return lis.size();
    }
};
```
###  考慮最佳解路徑
{% note warning %}
上面方法雖然可以正確求出LIS的長度，卻無法保證vector lis裡面儲存的是正確的序列（當最末端的LIS出現後，後面的元素會因為lower_bound插入的關係出現在序列的中間
解決方法：透過dp[]紀錄每一個元素出現的位置，可以求得正確的序列
{% endnote %}
key：下方第30-35行程式碼
```cpp=
#include <bits/stdc++.h>
using namespace std;
int n,L,dp[100005],temp;
vector<int> vec,lis,ans;

int main(){
    cin>>n;
    for(int i=0;i<n;i++){
        cin>>temp;
        vec.push_back(temp);
    }
    lis.push_back(vec[0]);
    dp[0] = 1;
    for(int i=1;i<n;i++){
        int ind = 0;
        if(vec[i]>lis.back()){
            lis.push_back(vec[i]);
            ind = lis.size();
            dp[i] = ind;
        }
        else{
            ind = lower_bound(lis.begin(), lis.end(),vec[i])-lis.begin();
            lis[ind] = vec[i];
            dp[i] = ind+1;//dp以1為起始
        }
    }
    L = lis.size();
    cout<<"LIS:"<<L<<endl;//輸出lis長度，但此時vector lis中的序列是錯誤的
    
    for(int i=n-1;i>=0;i--){
        if(dp[i] == L){
            ans.push_back(vec[i]);//利用vec裡的東西產生正確序列ans，捨棄lis
            L--;
        }
    }
    reverse(ans.begin(), ans.end());
    for(auto i: ans)cout<<i<<" ";
}
```
{% note info %}
範例測資：
8
10 9 2 5 3 7 101 6
得到的dp = {1 1 **1** 2 **2** **3** **4** 3}
我們要的就是上方粗體對應到的就是正確的LIS：2 3 7 101
{% endnote %}
## 最長共同子序列（LCS）
> 給定兩個英文字串，找出 LCS。（在兩個字串中各取出字串中字元，使兩者相等的最大長度—字母順序不可以改變）

### 不考慮最佳解路徑
定義：給定字串 s1,s2，dp[n][m]為s1長度為n、s2長度為m時的LCS
初始值：初始陣列dp為0
轉移：
$$dp[i][j] = dp[i-1][j-1]+1, if\ s1[i-1]=s2[j-1]$$$$dp[i][j] = max(dp[i-1][j],dp[i][j-1]), if\ s1[i-1]!=s2[j-1]$$
推導過程可以看：[資芽講義](https://www.csie.ntu.edu.tw/~sprout/algo2016/ppt_pdf/dynamic_programming_2.pdf)


以 leetcode [1143. Longest Common Subsequence](https://leetcode.com/problems/longest-common-subsequence/)為例：

```cpp=
class Solution {
public:

int longestCommonSubsequence(string text1, string text2) {
    int n1 = text1.size(), n2 = text2.size(),dp[n1+1][n2+1];
    memset(dp,0,sizeof(dp));

    for(int i=1;i<=n1;i++){
        for(int j=1;j<=n2;j++){
            if(text1[i-1]==text2[j-1]){
                dp[i][j] = dp[i-1][j-1]+1;
            }
            else{
                dp[i][j] = max(dp[i-1][j],dp[i][j-1]);
            }
        }
    }
    return dp[n1][n2];
}
};
```
### 考慮最佳解路徑
求出一組LCS的方法，可以利用一個二維陣列p[i][j] 紀錄dp[i][j]從何而來，由求LCS的過程中可以發現，dp[i][j]可以有三個來源(18-32行)，分別是：
1. 左上格（此時a[i]=b[j]) — 定義為0
1. 左格（此時 dp[i][j-1]>dp[i-1][j]）— 定義為1
1. 上格（此時 dp[i-1][j]>dp[i][j-1]）— 定義為2

紀錄完成後，透過遞迴或迴圈的方式回朔求得答案。從末端開始，如果遇到0 則往左上格移動，1 則往左格，2則往上格。

**利用遞迴求解**
```cpp=
void print(int i,int j){
    if(i==0||j==0)return;//邊界
    if(p[i][j]==0){
        print(i-1, j-1);
        cout<<a[i-1];
    }
    else if(p[i][j]==1)print(i, j-1);//左格
    else print(i-1, j);//上格
}
```
**利用迴圈求解**
因為是利用回朔法，輸出會是反序，因此新開一個陣列儲存答案
```cpp=
void print(int i,int j){
    if(!len)return;
    int l = len;
    char lcs[l];
    while(l>0){
        if(p[i][j]==0){
            l--;i--;j--;
            lcs[l] = a[i];
        }
        else if(p[i][j]==1)j--;//左格
        else i--;//上格
    }
    cout<<lcs;
}
```
完整版程式碼：
```cpp=
#include <bits/stdc++.h>
#define int long long
using namespace std;
string a,b;
int lenA,lenB,p[1005][1005],len;
void print(int i,int j);

signed main(){
    cin>>a>>b;
    lenA = a.length();
    lenB = b.length();
    
    int dp[lenA+1][lenB+1];
    memset(dp, 0, sizeof(dp));
    memset(p, 0, sizeof(p));//紀錄每一個dp元素從何而來
    //定義：0 左上格，1 左格，2上格。
    for(int i=1;i<=lenA;i++){
        for(int j=1;j<=lenB;j++){
            if(a[i-1]==b[j-1]){
                dp[i][j] = dp[i-1][j-1]+1;
                p[i][j] = 0;//left-top
            }
            else{
                if(dp[i-1][j]<dp[i][j-1]){
                    dp[i][j] = dp[i][j-1];
                    p[i][j] = 1;//left
                }
                else{
                    dp[i][j] = dp[i-1][j];
                    p[i][j] = 2;//top
                }
            }
        }
    }
    len = dp[lenA][lenB];
    cout<<len<<endl;
    print(lenA, lenB);
    cout<<endl;
}
//遞迴
void print(int i,int j){
    if(i==0||j==0)return;
    if(p[i][j]==0){
        print(i-1, j-1);
        cout<<a[i-1];
    }
    else if(p[i][j]==1)print(i, j-1);
    else print(i-1, j);
}
//迴圈
void print(int i,int j){
    if(!len)return;
    int l = len;
    char lcs[l];
    while(l>0){
        if(p[i][j]==0){
            l--;i--;j--;
            lcs[l] = a[i];
        }
        else if(p[i][j]==1)j--;
        else i--;
    }
    cout<<lcs;
}
```
## 題目＆資源
[學長講yee](https://slides.com/justinlai2003/dq-dp#/6)
https://leetcode.com/tag/dynamic-programming/
- [x] https://tioj.ck.tp.edu.tw/problems/1354
- [x] https://tioj.ck.tp.edu.tw/problems/1019
- [ ] https://codeforces.com/problemset/problem/1207/C
- [x] https://tioj.ck.tp.edu.tw/problems/1097
- [ ] https://codeforces.com/problemset/problem/1398/D
- [x] https://tioj.ck.tp.edu.tw/problems/1029
- [x] https://zerojudge.tw/ShowProblem?problemid=d038
- [x] https://zerojudge.tw/ShowProblem?problemid=d390
- [x] https://zerojudge.tw/ShowProblem?problemid=d133
- [x] https://zerojudge.tw/ShowProblem?problemid=a133
- [x] https://zerojudge.tw/ShowProblem?problemid=d674
