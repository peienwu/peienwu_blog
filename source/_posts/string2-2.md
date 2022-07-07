---
title: "[題解]NEOJ 265 欸迪的字串"
date: 2021-1-9
tags: 
    - 字串
    - 題解
categories: 字串題解
mathjax: true
---

### NEOJ 265 欸迪的字串
<!-- more -->
[題目連結](https://neoj.sprout.tw/problem/265/)
[Submission](https://neoj.sprout.tw/challenge/179036/)
> 題目敘述
> 給你字串S和字串T，求出S在T中出現的位置

這一題在**隨機演算法**也有出現過，當時是用Rolling Hash的方式透過扣掉Hash的方法比對字串是否相同（推一下公式就知道）。現在要用的是**KMP演算法**，很複雜不好想。

比較一下隨機跟KMP的時間，下方為KMP，上方則是隨機算法，兩者時間差了將近1倍，雖然兩者都可以在O(|T|)時間內進行匹配，但是建立Rolling Hash的常數是比較大的！
![](https://i.imgur.com/a7CPjyi.png)

#### 程式碼

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld long double
#define int long long
#define N 500005
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int n,m;

vector<int> KMP_match(char *S,int *F,char *T){
    vector<int> ans;            //回傳匹配相同地方
    int p = -1;                 //紀錄短字串有多少被匹配
    for(int i=0;T[i];i++){      //每一迴圈都讓T[i]被匹配到
        while(p!=-1 && S[p+1]!=T[i])
            p = F[p];           //使T[i]一定可以被匹配到
        if(S[p+1] == T[i])
            p += 1;             //T的第i個與S的p+1可以匹配
        if(!S[p+1]){            //S[p]已經匹配完成
            ans.push_back(i-p); //回推匹配開頭
            p = F[p];           //繼續下一輪匹配
        }
    }
    return ans;
}

//O(|S|)要配對的字串以及Fail Function
void KMP_build(char *S,int *F){
    int p = F[0] = -1;              //初始設定為-1
    for(int i=1;S[i];i++){          //1到接下來字元
        while(p!=-1 && S[p+1]!=S[i])
            p = F[p];               //無法繼續配對，尋找更短字串
        if(S[p+1] == S[i])          //配對成功(如都沒有一樣的就-1)
            p += 1;
        F[i] = p;                   //設定F[i]
    }
}

signed main(){
    Orz;
    char S[N],T[N];     //詢問短字串、長在串
    int F[N];           //失敗函數
    cin>>S>>T;
    KMP_build(S,F);
    vector<int> ans = KMP_match(S,F,T);
    if(ans.size()>0){
        cout<<ans[0];
        rep(i,1,ans.size()-1)cout<<" "<<ans[i];
    }
    cout<<endl;
}
```
