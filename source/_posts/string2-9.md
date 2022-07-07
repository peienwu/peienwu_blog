---
title: "[題解]TIOJ 1725 Massacre at Camp Happy"
date: 2021-1-9
tags: 
    - 字串
    - 題解
categories: 字串題解
mathjax: true
---

### TIOJ 1725 Massacre at Camp Happy
<!-- more -->
[題目連結](https://tioj.ck.tp.edu.tw/problems/1725)
[Submission](https://tioj.ck.tp.edu.tw/submissions/264017)

> 題目敘述：
定義字串 A 和 B「k-幾乎相同」代表把字串 A 的前 k 字元搬到最後面時，與 B 恰有一個字元相異。給你兩個長度 ≤ $10^6$ 的字串 A 和 B，求所有使 A 和 B「k-幾乎相同」成立的 k 值。

這一題好特別，一樣要用 $O(n)$ 的時間求出答案，對於每一個操作將前k個字元搬到後面，只能用 $O(1)$ 算出來到底符不符合一字元相異的條件。
以兩個相等長度的字串 $A,B$ 為例，長度為 $l$，假設 $A=aabba,B = aacba$，兩者相差一個字元的情況下，必定會滿足$l = LCP(A,B)+LCP(rev(A),rev(B))+1$，其中rev函數表示reverse。

我們要做的就是在 $O(1)$ 的時間內求出LCP，因為題目的字串 $A$ 要求不同的起始位置，因此我們假設一個非常特別的字串 $B@AA$ ，其中@為任意沒出現過的字元。只要計算這一個字串的Z函數，就表示了 $A$ 和 $B$ 的最長共同前綴，需要兩個A的原因是因為模擬不同的k-位移下情形。

![](https://i.imgur.com/5Ir8Hjt.png)

{% note success %}
**實作小細節**
程式碼的第42行可以試著推推看逆序之後的尾巴的索引值為何。我是利用列出幾個數字之後推出來的，不過應該可以寫成比較嚴謹的數學證明！

```cpp=
Z1[n+1+i]+Z2[2*n-i+1]+1 == n
```

{% endnote %}

#### 時間複雜度

$O(n)$ 建立z函數，$O(n)$ 枚舉每一個起始點，總時間為 $O(n)$。

#### 程式碼

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld long double
#define N 1000005
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int n,m,Z1[3*N],Z2[3*N];
char A[3*N],B[3*N];

void Z_algo(int *z,char *S){
    int l = 0,r = 0;
    z[0] = 0;
    for(int i=1;i<m;i++){
        z[i] = max(min(z[i-l],r-i),0);
        while(S[i+z[i]] && S[z[i]] == S[i+z[i]]){
            l = i;r = i+z[i];
            z[i]++;
        }
    }
}

signed main(){
    Orz;
    cin>>n>>A>>B;
    m = 3*n+1;
    B[n] = '@';
    //正常序列求Z value
    rep(i,n+1,2*n)B[i] = A[i-n-1];
    rep(i,2*n+1,3*n)B[i] = A[i-2*n-1];
    Z_algo(Z1,B);
    //逆序求Z value
    reverse(B,B+n);reverse(B+n+1,B+m);
    Z_algo(Z2,B);
    
    vector<int> vec;
    for(int i=0;i<n;i++){
        if(Z1[n+1+i]+Z2[2*n-i+1]+1 == n)vec.push_back(i);
    }
    if(vec.size()){
        cout<<"TAK"<<endl;
        for(auto i:vec)cout<<i<<" ";
        cout<<endl;
    }
    else cout<<"NIE"<<endl;
    
}
//[0,n-1][n,n][n+1,2n][2n+1,3n]
```
