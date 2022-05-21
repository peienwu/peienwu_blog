---
title: Educational Codeforces Round 103 (Rated for Div. 2)
date: 2021-1-29
tags: 
    - CF
    - 競賽
categories:
	- C++基礎主題
	- CF

mathjax: true
---

* Rating change:1137->1197<font color="#f00">(+60)</font>
* Problem solved: 1

<!--more-->

## Problem 1

### 題序：[點此](https://codeforces.com/contest/1476/problem/A)

{% note default%}
> You are given two integers 𝑛 and 𝑘.
>
> You should create an array of 𝑛 positive integers 𝑎1,𝑎2,…,𝑎𝑛 such that the sum (𝑎1+𝑎2+⋯+𝑎𝑛) is divisible by 𝑘 and maximum element in 𝑎 is minimum possible.
>
> What is the minimum possible maximum element in 𝑎?

> **Input**
> The first line contains a single integer 𝑡 (1≤𝑡≤1000) — the number of test cases.
>
> The first and only line of each test case contains two integers 𝑛 and 𝑘 (1≤𝑛≤10^9^; 1≤𝑘≤10^9^).
>
> **Output**
> For each test case, print one integer — the minimum possible maximum element in array 𝑎 such that the sum (𝑎1+⋯+𝑎𝑛) is divisible by 𝑘.
{% endnote %}

### 解題想法

一開始看到測資範圍：10^9^，就想到*O(n)*的時間複雜度是不可行的（有好多人在賽後因為這樣被**hack**）。一開始的想法是分段討論：n = k, n > k, n < k

```cpp=
#include <bits/stdc++.h>
using namespace std;
int t;
 
signed main(){
    cin>>t;
    while(t--){
        int n,k;cin>>n>>k;
        if(n==k){
            cout<<1<<endl;
            continue;
        }
        if(n>k){
            if(n%k==0)cout<<1<<endl;
            else cout<<2<<endl;
        }
        else{
            int temp = k/n;
            if(k%n==0)cout<<temp<<endl;
            else cout<<temp+1<<endl;
        }
    }
}
```

看過別人程式碼後，發現其實也可以這樣改：

```cpp=
#include<iostream>
using namespace std;
int main() {
    int t;
    cin>>t;
    while(t--){
        int n,k;cin>>n>>k;
        k = k*(int)((n+k-1)/k);//大於等於n的k的最小倍數
        if(k%n==0) cout<<k/n<<'\n';
        else cout<<k/n+1<<'\n';
    }
    return 0;
}
```

而官方解法則是完全的數學，把式子寫出來之後，直接O(1)的複雜度算出答案
{% note info %}
Let's denote 𝑠 as the sum of array 𝑎. From one side, since 𝑠 should be divisible by 𝑘 then we can say $𝑠=𝑐𝑓⋅𝑘$ From other side, since all 𝑎𝑖 are positive, then $𝑠≥𝑛$.

It's quite obvious that the smaller $𝑠$ — the smaller maximum $𝑎𝑖$ so we need to find the smallest $𝑐𝑓$ that $𝑐𝑓⋅𝑘≥𝑛$. Then
$$𝑐𝑓=⌈\frac{𝑛}{𝑘}⌉=⌊\frac{𝑛+𝑘−1}{𝑘}⌋$$

Now we now that $𝑠=𝑐𝑓⋅𝑘$ and we need to represent it as 𝑎1+⋯+𝑎𝑛 with maximum 𝑎𝑖 minimized. It's easy to prove by contradiction that maximum
$$𝑎𝑖≥⌈\frac{𝑠}{𝑛}⌉.$$

Moreover we can always construct such array 𝑎 that its sum is equal to 𝑠 and the maximum element is equal to $⌈\frac{𝑠}{𝑛}⌉$

As a result, the answer is
$$⌈\frac{𝑠}{𝑛}⌉=⌊\frac{𝑐𝑓⋅𝑘+𝑛−1}{𝑛}⌋, 𝑐𝑓=⌊\frac{𝑛+𝑘−1}{𝑘}⌋.$$
{% endnote %}
以下為官解：

```cpp=
#include<bits/stdc++.h>
using namespace std;
int t;

int main(){
  cin >> t;
  while(t--) {
    long long n, k;
    cin >> n >> k;
    long long cf = (n + k - 1) / k;
    k *= cf;
    
    cout << (k + n - 1) / n << endl;
  }
  return 0;
}

```

### 解題紀錄

{% note success %}
這一題共花了17分鐘才寫出來
可能是對題目的反應不夠快，沒有第一時間就想出解法
但第一題就應該是要很簡單的呀！
第一題是拚速度，速度快就可以排名比較前面(我看有許多人不到5分鐘就寫完了！)
{% endnote %}

## Problem 2

### 題序：[點此](https://codeforces.com/contest/1476/problem/B)

{% note default%}
>題目敘述
> > 給定兩個整數*n*,*k*，與*n*個整數*p~0~,p~1~,…𝑝~𝑛−1~  (1≤𝑝~𝑖~≤10^9^)*,其中p~0~代表物品的原始價格，𝑝~𝑖~則代表在第 𝑖-個月的價格變化。
> > *k*代表每個月的通脹係數不得超過 𝑘％，因此必須對𝑝~𝑖~**增加**一些值，使所有𝑝~𝑖~為整數且每個月的通脹係數不超過 𝑘％。
> > 試求最小的𝑝~𝑖~增加值為何？
>
> 範例測資
> ![](https://i.imgur.com/StpNbiI.png)

> >
> >輸入：
2
4 1
20100 1 202 202
3 100
1 1 1
輸出：
99
0

{% endnote %}

### 解題想法

依照題意，開一個長度為n的陣列，由第一天的上升價格開始，依序計算看會不會超過k%的膨脹係數。
{% note success %}
Let sum = p$_0$+p$_1$+$\cdots$+p$_{i-1}$
x 為 p$_{i}$要加的整數
則：
$$\frac{p_i}{sum+x} \le \frac{k}{100}$$
經過通分後：
$$ x = \lceil\frac{100\times p_i-sum\times k}{k}\rceil\ge0$$
{% endnote %}

因此可以寫出以下程式碼

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define int long long
using namespace std;
int t,arr[105];
 
signed main(){
    ios;
    cin>>t;
    while(t--){
        int n,k,base,sum =0,ans = 0;
        cin>>n>>k>>base;
        sum=base;
        for(int i=0;i<n-1;i++)cin>>arr[i];
        for(int i=0;i<n-1;i++){
            if(100*arr[i]>k*sum){
                int temp = ceil((arr[i]*100-sum*k)/(double)k);
                ans+=temp;
                sum+=temp;
            }
            sum+=arr[i];
        }
        cout<<ans<<endl;
    }
}

```

{% note warning%}

```cpp=
int temp = ceil((arr[i]*100-sum*k)/(double)k);
```

這一段程式碼可以改成：

```cpp=
int temp = (arr[i]*100-sum*k+k-1)/k;
```

也可以達到**取上高斯**的效果
後者時間快上許多
{% endnote %}

### 解題紀錄

當初在寫的時候忘記要在減完後除以k，因此吃了三次WA
所以以後知道，在寫題目之前，最好都先把數學式寫得清楚
在解題的時候才不會犯下這種錯！
另外，我現在才知道，取下高斯就是除法的*double*轉*int*就可以了
上高斯的話，除了用 *ceil()* 函式，還可以用上面那個方式來達到同樣的效果。
這一次rating change 剛好差了3就到1200，真是可惜，還要再打一場才可以升去

{% note danger%}
這一場主要學到的重點應該是怎麼樣把取上高斯轉換為下高斯（不用ceil()取）!
{% endnote %}
