---
title: "[題解]TIOJ 1306 字串中的字串"
date: 2021-1-9
tags: 
    - 字串
    - 題解
categories: 字串題解
mathjax: true
---
### TIOJ 1306 字串中的字串
<!-- more -->
[題目連結](https://tioj.ck.tp.edu.tw/problems/1306)
[Submission](https://tioj.ck.tp.edu.tw/submissions/263572)
> 題目敘述
裸字串匹配。對於每個詢問輸出 $S$ 在 $T$ 中出現過幾次。

裸KMP字串匹配，統計出現次數。可以嘗試不要看資料自己寫一次，會更了解KMP算法！

#### 程式碼

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld long double
#define int long long
#define N 10005
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int t,n,F[N];;
char T[N],S[N];

int KMP_match(char *S,char *T,int *F){
    int p = -1,ans = 0;
    for(int i=0;T[i];i++){
        while(p!=-1 && S[p+1]!=T[i])
            p = F[p];
        if(S[p+1] == T[i])
            p += 1;
        if(!S[p+1]){
            ans += 1;
            p = F[p];
        }
    }
    return ans;
}

void KMP_build(char *S,int *F){
    int p = F[0] = -1;
    for(int i=1;S[i];i++){
        while(p!=-1 && S[p+1]!=S[i])
            p = F[p];
        if(S[p+1] == S[i])
            p += 1;
        F[i] = p;
    }
}

void solve(){
    while(n--){
        memset(F,0,sizeof(F));
        cin>>S;
        KMP_build(S,F);
        cout<<KMP_match(S,T,F)<<endl;
    }
}

signed main(){
    Orz;
    cin>>t;
    while(t--){
        cin>>T>>n;
        solve();
    }
}
```
