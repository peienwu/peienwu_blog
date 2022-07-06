---
title: "[題解]NEOJ 400 向左轉向右轉"
date: 2021-1-6
tags: 
    - 計算幾何
    - 題解
categories: 計算幾何題解
mathjax: true
---

### 向左轉向右轉
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/400/)
[Submission](https://neoj.sprout.tw/challenge/178524/)

> 題目敘述
> 給你平面上n個點，依序走訪每一個點，試問走訪過程中共執行幾次的左轉、右轉以及迴轉。

很特別，計算幾何讓電腦可以處理平常我們所看到的平面圖形，可以利用向量內積、外積等方式判斷方向。這一題最重要的就是**方向函數**。傳入3個點$(A,B,O)$，方向函數會會回傳$\stackrel\longrightarrow{OA}\times \stackrel\longrightarrow{OB}$ 的正負數值。

下圖為外積$\stackrel\longrightarrow{OA}\times \stackrel\longrightarrow{OB}$ 的結果，當 $\sin\theta$的結果為負，也就是下圖的情況，從B走到A就需要往左邊走；反之亦然。
![](https://i.imgur.com/eDNRLhm.png)

至於如何判斷當兩個向量的方向呈現一直線時，也就是外積回傳的值為0時（$\sin\theta = 0$），應該是同向還是異向呢？這時候就需要搭配向量內積（這我想了很久），因為內積公式是$A\cdot B = |A||B|\cos\theta$，將兩個向量內積之後就可以很明確的判斷到底是朝原本的方向走，還是反方向的行走！

{% note info %}
**內積、外積公式**
有一點數學，不過蠻有趣的。可以利用$\sin$與$\cos$達到計算角度的目的，利用兩者不同的值域，互相搭配，就可以更輕鬆的進行判斷！注意到外積的正負就代表著A到B是順時針或是逆時鐘。
$$A\cdot B = |A||B|\cos\theta = A_xB_x+A_yB_y\\A\times B = |A||B|\sin\theta = A_xB_y-A_yB_x$$

<br>

**方向函數**

當我們要判斷方向的時候，會利用正弦函數，逆時針正、順時針為負進行判斷！

```cpp=
int dir(pt a, pt b, pt o) {
    int cross = (a - o) ^ (b - o);
    if(fabs(cross) <= eps) return 0;
    else if(cross > 0) return 1;
    else return -1;
}
```

注意到此時在判斷是否為平行的時候（cross==0），使用到$fabs()$這個函數，目的是為了避免誤差而導致判斷錯誤，因此需要進行誤差的處理（其實不用也沒差啦，只是這樣嚴謹一點）
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
#define N 1003
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
vector<pt> a;
int dir(pt a, pt b, pt o) {
    int cross = (a - o) ^ (b - o);
    if(fabs(cross) <= eps) return 0;
    else if(cross > 0) return 1;
    else return -1;
}

int n,t;

signed main(){
    Orz;
    cin>>n;
    a.resize(n+2);
    rep(i,1,n)cin>>a[i].x>>a[i].y;
    int right = 0,left = 0,turn = 0;
    pt pre = a[1],from = a[2];
    for(int i=3;i<=n;i++){
        int ori = dir(a[i],from,pre);
        if(ori == 1)right+=1;
        else if(ori == -1)left+=1;
        else if(ori == 0 && ((a[i]-from)*(from-pre))<0)turn+=1;
        pre = from;from = a[i];
    }
    cout<<left<<" "<<right<<" "<<turn<<endl;
}
```
