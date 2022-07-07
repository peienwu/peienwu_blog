---
title: "[題解]NEOJ 264 欸迪的字串"
date: 2021-1-9
tags: 
    - 字串
    - 題解
categories: 字串題解
mathjax: true
---

### NEOJ 264 欸迪的字串
<!-- more -->
[題目連結](https://neoj.sprout.tw/problem/264/)
[Submission](https://neoj.sprout.tw/challenge/179060/)
> 題目敘述
> 給你字串S，求一個最短字串T的長度，滿足經由複製若干次後接起來會是字串S。

這一題在一開始看到感覺好難，不過在慢慢理解 Z_Algorithm 之後，在講義中發現到這個問題也可以用這個演算法來解決！因為Z函數是滿足從i開始的最長前綴，因此當 $i$ 為 字串長度 $n$ 的因數的時候，只要 $i+Z[i+1]=n$ 成立，就必定可以用 $S[1:i]$ 組成字串 $S$ 。實作上要注意字串是0-base，i+1就相等於字串第i個位置，處理上要特別注意！

#### 程式碼

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld long double
#define N 500005
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int n,m;

void Z_algo(char *S,int *Z){
    int l = 0,r = 0;
    Z[0] = 0;
    for(int i=1;S[i];i++){
        Z[i] = max(0,min(Z[i-l],r-i));
        while(S[Z[i]]&&S[Z[i]] == S[i+Z[i]]){
            l = i;r = i+Z[i];
            Z[i]++;
        }
    }
}

signed main(){
    Orz;
    char s[N];cin>>s;
    int Z[N],n = strlen(s),ans = n;
    Z_algo(s,Z);
    
    rep(i,1,n-1){
        if(n%i==0 && i+Z[i]==n){
            ans = i;
            break;
        }
    }
    cout<<ans<<endl;
}
```
