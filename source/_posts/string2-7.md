---
title: "[題解]TIOJ 1321 好多回文 ndromePali"
date: 2021-1-9
tags: 
    - 字串
    - 題解
categories: 字串題解
mathjax: true
---

### TIOJ 1321 好多回文 ndromePali
<!-- more -->
[題目連結](https://tioj.ck.tp.edu.tw/problems/1321)
[Submission](https://tioj.ck.tp.edu.tw/submissions/263962)
> 題目敘述
給一個長度 ≤ $10^6$ 的字串 A，問有幾種把該字串的某前綴搬到最後面的方法，使得最後的字串為回文。
(提示:可以用類似 Z 函數的概念計算出以某字元為中心的最長回文長度。)

這一題搞得我快掛了QQQ。為什麼呢？看看以下照片就知道了：

![](https://i.imgur.com/i8Gx5Yo.png)

根據上面的「提示」，要用類似Z函數的概念把以某點為中心的最長回文算出來。一個Naive裸的作法，把用 $O(n)$ 枚舉每一個點為分割點，把分割點前面的子字串接到後面，用 $O(n)$ 的時間看是否為一回文字串。如此一來時間複雜度為 $O(n^2)$ ，對這一題來說顯然是不可行的，因為字串長度達到 $10^6$！

當我們用Manacher’s Algorithm $O(n)$ 的時間每一個字元的最大回文之後，就可以枚舉每一個可以作為分割點的地方，檢查回文的狀況。討論分割點在前半部分的情況下，也就是把短的（長度小於一半）前綴搬到後面的狀況。當分割的索引值為 $i$ ，必須保證 $s[0:i]$ 跟 $s[i:2i]$ 對稱，移到字串尾巴才能成為回文。除此之外，也必須滿足 $Z[mid+i] = mid-i$ 的情況，以就是剩下不是分割點兩側的字串必須是回文，把前綴接到後面之後才能對稱！

實作上很多細節，我DEBUG超久最後才想到不能枚舉所有字元，會出事（就像上面的三色WA），只要枚舉以 $'.'$ 為分割點的情況（分割在字元上就不行），還有很多細節，WA很多次才會知道XD

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
int n,m,Z[2*N];
bool ans[2*N];
char S[2*N],T[N];
//T原字串、S插入點字串

void Longest(){
    n = strlen(T);m = 2*n+1;
    memset(S,'.',m);
    for(int i=0;i<n;i++)S[2*i+1] = T[i];
    Z[0] = 1;
    int l = 0,r = 0;
    for(int i=1;i<m;i++){
        Z[i] = max(min(Z[2*l-i],r-i),1);
        while(i-Z[i]>=0 && i+Z[i] < m && S[i+Z[i]]==S[i-Z[i]]){
            l = i;r = i+Z[i];
            Z[i]++;
        }
    }
}

signed main(){
    Orz;
    cin>>T;
    Longest();
    
    for(int i=0;i<m;i++)Z[i]--;
    memset(ans,0,sizeof(ans));
    
    int mid = (m-1)/2,i;
    
    if(Z[mid] == mid)ans[0] = 1;
    for(i=2;i<mid;i+=2){
        if(Z[i] == i && Z[mid+i] == mid-i){
            ans[(i/2)] = 1;
        }
    }
    for(;i<m;i+=2){
        if(Z[i] == m-i-1 && Z[i-mid] == i-mid){
            ans[(i/2)] = 1;
        }
    }
    
    int sum = 0;
    for(int i=0;i<n;i++)if(ans[i])sum++;
    if(sum == 0)cout<<"none"<<endl;
    else{
        cout<<sum<<":";
        for(int i=0;i<n;i++)if(ans[i])cout<<" "<<i;
        cout<<endl;
    }
}
```
