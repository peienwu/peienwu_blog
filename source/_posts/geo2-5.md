---
title: "[題解]TIOJ 1178 Convex Hull"
date: 2021-1-6
tags: 
    - 計算幾何
    - 題解
categories: 計算幾何題解
mathjax: true
---

### TIOJ 1178 Convex Hull
<!--more-->
[題目連結](https://tioj.ck.tp.edu.tw/problems/1178)
[Submission](https://tioj.ck.tp.edu.tw/submissions/262532)

> 題目敘述
給定n個二維平面的點，找出位在凸包上的所有點的個數

最小凸多邊形 = 凸包，要找出能包住所有點的最小凸多邊形，簡稱凸包。聽說最好寫的凸包演算法是：Andrew's Monotone Chain，翻成中文叫做Andrew's 單調鍊？有一點單調+鍊的味道。下圖是我用照片合成起來的GIF，大致模擬出使用Andrew's Monotone Chain 找凸包的方法。

![](https://i.imgur.com/YUOC9xZ.gif)

{% note default %}
**Andrew's Monotone Chain**
這個演算法的時間複雜度是 $O(n\log n)$，空間複雜度 $O(n)$，資料說它可以解決了凸包有重疊的點、共線的點、退化成線段和點的情況。它的名字叫做「單調鍊」，要維護一個有點像單調隊列的東西，對於在容器中第 $i$ 個位置的點都滿足 $\stackrel\longrightarrow{P_i P_{i+1}}\times \stackrel\longrightarrow{P_{i+1} P_{i+2}} > 0$ ，如果有點做外積後的結果小於等於0，則它會被pop掉（這是依照上圖逆時針完成凸包的描述，如果方向相反則會變號）。
<br>

以下是此演算法的執行步驟：

1. 先把所有的點按照 $(x,y)$ 排序
2. 將下凸包圍出來，有點像維護單調隊列，對所有新加入的點i計算點i-2、i-1與i之間的外積，如果不符合情況代表圍不到新加入的點，需要將舊的點pop出來
3. 將原本已經排序好的點逆序
3. 再把上凸包由x座標大到小圍出來，將上下合併就是凸包了（必須注意起終點被push的次數問題）！
{% endnote %}

一般會用一個vector儲存在凸包上面的點（不包含在邊上的點，只有位於轉折點的點），在頭尾的部分（x座標最大與最小）需要特別處理，讓每一個點最多近到vector一次。

{% note success %}
**實作細節**
以下是確認是否需要將vector中元素pop出來的關鍵，對向量$\stackrel\longrightarrow{OA}\times \stackrel\longrightarrow{OB}$ 做外積的結果，必須排除外積結果為0的情況，如果將0也納入，會造成一個點被push進去很多次，在數量和計算上出現問題。

```cpp=
bool check(pt a,pt b,pt o){
    int cross = (a - o)^(b - o);
    return cross >= 0;         //這裡很關鍵，別吃WA
}
```

除此之外，上凸包在範圍限制上是需要注意的。假設x座標最大的點i，當在圍上凸包的過程中i是不可以被pop出去的，因此vector的大小必須大於下凸包的大小。

凸包使用第i-1跟第i個點的向量去看第i到第i+1個點的向量，決定一個點要不要被推入vector中。當我們逆序從x座標最大的點往前看時，要確保每一輪結束之後在i點後都必須要有至少一個點，設定hull.size() > down_hull的原因是防止在下凸包的點被圍上凸包的過程更新到。

```cpp=+
int down_hull = hull.size();    //圍上凸包的程式碼片段
for(auto i: p){
    while(hull.size() > down_hull 
        && check(i,hull[hull.size()-1],hull[hull.size()-2])){
        hull.pop_back();
    }
    hull.push_back(i);
}
```

{% endnote %}
以下是AC Code:

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
    return cross >= 0;                              //這裡很關鍵，別吃WA
}

int n,t;

vector<pt> convex_hull(){
    vector<pt> hull;
    sort(p.begin(),p.end(),cmp);       //首先對x進行排序
    for(auto i : p){                   //依序走訪，如果遇到外積<0則不在凸包上
        while(hull.size()>=2 && check(i,hull[hull.size()-1],hull[hull.size()-2])){
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
    cin>>n;
    p.assign(n,{0,0});
    rep(i,0,n-1)cin>>p[i].x>>p[i].y;
    vector<pt> hull = convex_hull();
    cout<<hull.size()-1<<endl;
}
```
