---
title: "[題解]TIOJ 1515 Problem E. 似曾相識"
date: 2021-1-9
tags: 
    - 字串
    - 題解
categories: 字串題解
mathjax: true
---

### TIOJ 1515 Problem E. 似曾相識
<!-- more -->
[題目連結](https://tioj.ck.tp.edu.tw/problems/1515)
[Submission](https://tioj.ck.tp.edu.tw/submissions/264483)
> 題目敘述
裸後綴數組 LCP。

這題我想好久，一直想不通轉移的條件以及k-1的原因，不過最後還是想通了！這一題只要把 $lcp$ 求出來之後，找最大值就是題目要求的答案。

#### 程式碼

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld long double
#define int long long
#define N 200005
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define all(x) x.begin(),x.end()
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int n,sa[N];

struct pt{
    int x,y,id;
    bool operator==(pt b){
        if(x==b.x && y==b.y)return true;
        return false;
    }
};
//第一位、第二位比較以及後綴編號

bool cmp(pt a,pt b){
    if(a.x != b.x)return a.x < b.x;
    return a.y < b.y;
}

void suffix_array(string S){
    n = S.size();
    int lg = 32-__builtin_clz(n);
    //回傳第一個1之前0的個數（二進位）
    vector<pt> cur(n,{0,0,0});
    vector<int> rk(n+1,-1);
    rep(i,0,n-1)cur[i] = {S[i],0,i};
    rep(p,0,lg){
        int k = 1 << p;         //現在倍增的大小
        sort(all(cur),cmp);
        rk[cur[0].id] = 0;
        rep(i,1,n-1)
            rk[cur[i].id] = (cur[i-1]==cur[i] ? rk[cur[i-1].id]:i);
            //設定rk，與前一個相同則設定跟前一個一樣
        rep(i,0,n-1){
            cur[i] = {rk[i],rk[min(n,i+k)],i};
        }
    }
    sort(all(cur),cmp);
    rep(i,0,n-1)sa[i] = cur[i].id;
}

vector<int> LCP(string s){
    vector<int> rk(n,0),lcp(n,0);
    rep(i,0,n-1)rk[sa[i]] = i;      //利用sa反函數得到rk
    int k = 0;
    rep(i,0,n-1){
        if(k)k--;
        if(rk[i] == n-1)continue;   //rk[n-1]未定義
        int j = sa[rk[i]+1];        //下一名後綴從何開始
        while(i+k<n && j+k<n && s[i+k] == s[j+k])k++;
        lcp[rk[i]] = k;
    }
    return lcp;
}

signed main(){
    Orz;
    cin>>n;cin.ignore();
    string S;getline(cin,S);
    suffix_array(S);
    vector<int> lcp = LCP(S);
    
    int ans = 0;
    rep(i,0,n-1)ans = max(lcp[i],ans);
    cout<<ans<<endl;
}
```
