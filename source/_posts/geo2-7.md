---
title: "[題解]NEOJ 790 來吧，遊戲開始了"
date: 2021-1-6
tags: 
    - 計算幾何
    - 題解
categories: 計算幾何題解
mathjax: true
---

### 來吧，遊戲開始了
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/790/)
[Submission](https://neoj.sprout.tw/challenge/178691/)
[GGB模擬](https://www.geogebra.org/graphing/h4fxdquw)

> 題目敘述
給你二維平面上n個點(n≤2400)，每一個點座標皆不相同，求出總共可以圍出多少個三角形？

這是NEOJ上的加分題，好像是一個題組吧，反正總共有三題，這是第一題。如果 $O(n^3)$ 的枚舉，複雜度會爆炸（量級約$10^{10}$），根據電神的說法，這一題要用極角排序以及雙指標找到共線，接著就可以利用排列組合把因為共線而不能形成三角形的組合扣掉，就是答案了。

![](https://i.imgur.com/t5TF96I.png)

這一題的核心概念是找共線，具體來說的作法是枚舉每一個點的同時，以它為原點對其他的點進行排序，如果遇到有相同的極角座標表示這些點共線，同時利用陣列cnt[x]統計共線點數為x的線段總共有幾條。

以下的GIF就是大致上程式執行的樣子。因為一條長度為x的線段會因為枚舉x次的關係，在最後扣掉的情況會重複x次因此需要除掉。

![](https://i.imgur.com/MVfEx4i.gif)

{% note primary %}
**共線與三角形**

一般情況下（任三點不共線），總共可以形成 $C^n_3$ 個三角形，如果有一條m個點共線的情況下（其他點不共線），則可以形成的三角形數量就必須扣除共線限制的情況，變成 $C^n_3-C^m_3$ 個三角形。
{% endnote %}

時間複雜度為：枚舉每一個點 $O(n)$，極角排序 $O(n\log n)$，總時間複雜度 $O(n^2\log n)$

{% note default %}
**實作小細節**

**1. 維護共線連續區間**

我們要想辦法讓有共線的點們所在位置是一個連續的位置。三個點共線可能為在對角線的象限中，也就是點差了180度，如此一來就沒辦法讓共線的點為在連續的區間。為了達到這個目的，我們將所有位於下半平面的點都移到上半平面（在上半平面找到有相同 $\tan\theta$ 值的位置），接著就能利用雙指針找極角座標排序後有相同極角的區間之最大值！

**2. 特例判斷**

如果有一點y座標為0但x座標為負，要將其移到x軸正向的地方，不能把這種情況涵蓋為一般情況，否則原本在x軸正向的點會被移到x軸負向，沒有達到預期的效果。
{% endnote %}

以下是AC Code：

```cpp=
#include <bits/stdc++.h>
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define rep(i,a,b) for(int i=a;i<=b;i++)
#define pii pair<int,int>
#define pdd pair<double,double>
#define int long long
#define ll long long
#define ld long double
#define N 100001
#define all(x) x.begin(),x.end()
#define eps 1e-9
#define x first
#define y second

using namespace std;

struct pt{
    int x,y;
    bool operator < (pt b){
        if(x == b.x)return y < b.y;
        return x < b.x;
    }
    bool operator > (pt b){
        if(x == b.x)return y > b.y;
        return x > b.x;
    }
    bool operator == (pt b){
        if(x-b.x == 0 && y-b.y == 0)return true;
        return false;
    }
    pt operator+(pt b) {return {x + b.x, y + b.y};} //向量相加
    pt operator-(pt b) {return {x - b.x, y - b.y};} //向量相減
    int operator^(pt b) {return x * b.y - y * b.x;} //向量外積cross
    int operator*(pt b) {return x * b.x + y * b.y;} //向量內積dot
};

vector<pt> p;
vector<int> cnt;
int n,ans = 0;

bool cmp(pt a, pt b){
    bool f1 = a < pt{0,0};
    bool f2 = b < pt{0,0};
    if(f1 != f2)return f1 < f2;
    return (a ^ b) > 0;
    //逆時針將點進行極角排序，從270度開始逆時針
}

//用cnt[i]統計區間長度為i的線段數量
void solve(pt id){
    vector<pt> pp;
    for(auto i : p){                         //以id為原點
        pt cur = i-id;
        if(cur == pt{0,0})continue;
        if(cur.y < 0){cur.x = -cur.x;cur.y = -cur.y;}
        if(cur.x < 0 && cur.y==0){cur.x = -cur.x;}
        pp.push_back(cur);
    }
    sort(all(pp),cmp);                      //將id當作原點進行排序
    int p1 = 0,p2 = 0,len = pp.size();      //雙指針找共線區間
    while(p1 < n-1){                        //最大化區間
        while(p2+1 < len && (pp[p1]^pp[p2+1]) == 0)p2++;
        cnt[p2-p1+2]+=1;
        p1 = p2+1;
    }
}

signed main(){
    Orz;
    cin>>n;
    p.assign(n,{0,0});
    cnt.resize(n+1,0);
    rep(i,0,n-1)cin>>p[i].x>>p[i].y;
    rep(i,0,n-1)solve(p[i]);
    int ans = (n*(n-1)*(n-2))/6;
    rep(i,3,n)ans-=(cnt[i]*(i-1)*(i-2))/6;
    cout<<ans<<endl;
}
```
