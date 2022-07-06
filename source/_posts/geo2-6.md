---
title: "[題解]NEOJ 402 最小凸多邊形"
date: 2021-1-6
tags: 
    - 計算幾何
    - 題解
categories: 計算幾何題解
mathjax: true
---

### 最小凸多邊形
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/402/)
[Submission](https://neoj.sprout.tw/challenge/178589/)

> 題目敘述
找出二維平面上n個點的凸包所圍出來的面積為何？

跟上一題類似，在找到全部在凸包上面的點後，就可以利用**有向面積**把凸包面積算出來，有一個公式可以計算多邊形面積，利用外積得到正負值，轉一圈後得到面積！對於多邊形的頂點 $P_0,P_1,...,P_{n-1},P_n=P_0$ 的面積如下：
$$Area = \frac{1}{2}\sum_{i=0}^{n-1}\stackrel\longrightarrow{P_i}\times \stackrel\longrightarrow{P_{i+1}}$$

其中最後一個點會回到起點，形成一個封閉的迴路。

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
        if(x-b.x<=eps && y-b.y<=eps)return true;
        return false;
    }
    pt operator+(pt b) {return {x + b.x, y + b.y};} //向量相加
    pt operator-(pt b) {return {x - b.x, y - b.y};} //向量相減
    int operator^(pt b) {return x * b.y - y * b.x;} //向量外積cross
    int operator*(pt b) {return x * b.x + y * b.y;} //向量內積dot
};
bool cmp(pt a, pt b){
    if(a.x == b.x)return a.y < b.y;
    return a.x < b.x;
}

vector<pt> p;

bool check(pt a,pt b,pt o){
    int cross = (a - o)^(b - o);
    return cross >= 0;
}

int n,t;

vector<pt> convex_hull(){
    vector<pt> hull;
    sort(p.begin(),p.end(),cmp);       //首先對x進行排序
    for(auto i : p){                   //依序走訪，如果遇到外積<0則不在凸包上
        while(hull.size() > 1 && check(i,hull[hull.size()-1],hull[hull.size()-2])){
            hull.pop_back();
        }
        hull.push_back(i);             //在凸包hull的每一點都符合外積小於0
    }
    int down_hull = hull.size();
    hull.pop_back();                   //x最大的點會在凸包上，不用做兩次先pop一次
    reverse(p.begin(),p.end());        //將所有點逆序之後做一次上面的凸包
    for(auto i: p){
        while(hull.size() > down_hull && check(i,hull[hull.size()-1],hull[hull.size()-2])){
            hull.pop_back();
        }
        hull.push_back(i);
    }
    return hull;                       //起點會經過兩次，剛好來算有向面積
}

signed main(){
    Orz;
    cin>>t;
    while(t--){
        cin>>n;
        p.assign(n,{0,0});
        rep(i,0,n-1)cin>>p[i].x>>p[i].y;
        vector<pt> hull = convex_hull();
        int area = 0,len = hull.size();
        for(int i=0;i<len-1;i++)area += (hull[i]^hull[i+1]);
        cout<<fixed<<setprecision(1)<<((ld)area/2)<<endl;
    }
}
```
