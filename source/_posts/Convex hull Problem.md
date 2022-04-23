---
title: 牛牛牧場問題
date: 2022-1-30
tags: 
    - 演算法
    - 動態規劃
categories:
    - C++進階主題
    - 計算幾何

mathjax: true
---

## 題序

從前從前有一個人名叫thanksone，他與他的兩個哥哥thankstwo以及thanksthree以及他的弟弟thankszero一起開了一家「牛牛牧場」，用來養牛以及一些奇怪的東西。

有一天，他發現他的牛牛少了很多隻，不知道是跑去了哪裡，他很難過，因此他想要建造一個圍籬，讓所有牛牛都跑不出去。於是他拜託他的兩個哥哥thankstwo以及thanksthree去市集買了$n$個用來架設圍籬的木樁，並要求他們把這些木樁架設在他指定的位置。
<!--more-->

接著，他拜託他的弟弟thankszero也去買了條超長的圍籬，並要求thankszero利用這條超長圍籬將$n$個木樁都圍起來，才能讓牛牛有盡量大的活動空間。thankszero的數學比起他的哥哥thanksone還要糟，因此他需要拜託你幫忙計算一下當他圍好圍籬之後，**有多少個木樁會剛好落在這條圍籬上**。除此之外，他還希望你能給他所有在圍籬上的木樁的位置，讓他知道固定圍籬的木樁的位置。

最後，thanksone看到了你驚人的計算能力，於是他要求你計算出**圍籬所圍出來的面積**有多大，讓他能知道他的牛牛有多少的活動空間。

![](https://i.imgur.com/empMiPg.jpg=400x)

## INPUT

第一行有一個正整數$n(3≤n≤2⋅10^5)$，表示有$n$個木樁在莊園上
接下來有$n$行，每行有兩個整數 $x$ 及 $y$（$−10^9≤x,y≤10^9$），以一個空白隔開，用以代表一個木樁在座標軸上的位置。

保證輸入的每一點都不會重複。

## OUTPUT

第一行請輸出一個整數$k$：圍欄上木樁的個數（包含圍欄邊上的木樁）
接下來輸出$k$行，每一行為圍欄上木樁的座標，由$x$座標由小到大輸出，如果$x$座標相同則按照$y$座標大小輸出。
注意每一個木樁最多只能被輸出一次。
最後一行輸出圍欄所圍出的面積大小，資料四捨五入取到整數位。

## SUBTASK

40分：n ≤ 100

60分：沒有特別限制

## 範例測資1

### input

```
7
2 2
2 0
1 1
2 4
2 3
4 2
1 3
```

### output

```
5
1 1
1 3
2 0
2 4
4 2
7
```

## 範例測資2

### input

```
4
1 2
3 2
2 2
5 2
```

### output

```
4
1 2
2 2
3 2
5 2
0
```

從範例測資1可以發現，thanksone可以將下圖木樁$B,F,D,G,C$圍起來，能獲得最大的牛牛牧場面積：
![](https://i.imgur.com/2nD6cBt.png)

範例測資2可以看到，thanksone圍出來的牛牛牧場面積為 $0$：
![](https://i.imgur.com/GSQnKXN.png)

## 題目連結

[OJDL](http://ojdl.ck.tp.edu.tw/problem/7142)

## 題解

這一題就是凸包裸題，輸出所有包含在凸包上的點（頂點跟邊都要）。

### subtask 1

保證所形成的凸包面積不為0，所以做完凸包之後將邊上的點都輸出出來，之後再計算凸包面積就好。

### subtask 2

做完凸包可能會有點重複的問題，所以將所有點排序之後，記得去重，好好處理就可以AC了。

### AC code

```cpp=
#include <bits/stdc++.h>
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
    bool operator < (pt const &b){
        if(x == b.x)return y < b.y;
        return x < b.x;
    }
    bool operator > (pt const &b){
        if(x == b.x)return y > b.y;
        return x > b.x;
    }
    bool operator == (pt const &b){
        if(x-b.x == 0 && y-b.y == 0)return true;
        return false;
    }
    pt operator+(pt const &b) {return {x + b.x, y + b.y};} //向量相加
    pt operator-(pt const &b) {return {x - b.x, y - b.y};} //向量相減
    int operator^(pt const &b) {return x * b.y - y * b.x;} //向量外積cross
    int operator*(pt const &b) {return x * b.x + y * b.y;} //向量內積dot
};

struct comp{
    bool operator()(pt const &a,pt const &b)const{
        if(a.x == b.x)return a.y < b.y;
        return a.x < b.x;
    }
};

vector<pt> p,temp,pp;
vector<int>  cnt;
set<pt,comp> s;
int n,ans = 0;

bool cmp(pt a, pt b){
    if(a.x == b.x)return a.y < b.y;
    return a.x < b.x;
}

bool check(pt a,pt b,pt o){
    pt aa = a - o;
    pt bb = b - o;
    return (aa^bb) > 0;
}

vector<pt> solve(){
    sort(all(p),cmp);
    vector<pt> h;
    for(pt i : p){
        while(h.size()>=2 && check(i,h[h.size()-1],h[h.size()-2]))
            h.pop_back();
        h.push_back(i);
    }
    int sz = h.size();
    h.pop_back();
    reverse(all(p));
    for(auto i : p){
        while(h.size()>sz && check(i,h[h.size()-1],h[h.size()-2]))
            h.pop_back();
        h.push_back(i);
    }
    return h;
}

signed main(){
    cin>>n;
    p.resize(n,{0,0});
    rep(i,0,n-1)cin>>p[i].x>>p[i].y;
    vector<pt> hull = solve();
    int area = 0,sz = hull.size();
    rep(i,0,sz-2)area += (hull[i]^hull[i+1]);
    for(auto i : hull)s.insert(i);
    cout<<s.size()<<endl;
    for(auto i : s)cout<<i.x<<" "<<i.y<<endl;
    cout<<((area%2)?(area/2)+1:(area/2))<<endl;

}
```
