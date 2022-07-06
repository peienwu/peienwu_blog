---
title: "[題解]NEOJ 401 線段相交"
date: 2021-1-6
tags: 
    - 計算幾何
    - 題解
categories: 計算幾何題解
mathjax: true
---

### 線段相交
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/401/)
[Submission](https://neoj.sprout.tw/challenge/178537/)
線段相交 = ~~線段香蕉~~，自動選字永遠都是香蕉，有點煩XDD

如何判斷兩線段是否相交？首先需要一個函數可以判斷點是否在一個線段上，如此一來就可以判斷端點在另一條線段上的特殊情況。以下程式碼為判斷點$P_o$ 是否在 $\overline{P_aP_b}$ 上。利用向量外積可以判斷兩線段是否平行，而使用內積公式可以判斷$P_o$是否在線段中，而非線段的兩側！

```cpp=
bool onseg(pt a, pt b, pt o){       //o是否在ab線段上
    int cross = (a - o) ^ (b - o);  //是否平行
    int dot = (a - o) * (b - o);    //是否在線段中
    return (cross == 0)&&(dot <= 0);
}
```

說明：由點$P_o$指向a和b的向量必須呈現180度角（也就是異向），才可確保在ab線段中（跟a,b重合也算是跟ab線段相交）。

接下來是主要的部分，首先先確認4個端點是否恰好在另外一條線段上，判斷完之後就是處理一般相交的情況。若線段 $\overline{P_1P_2}$ 與 $\overline{P_3P_4}$ 相交，則點 $P_1$ 與點 $P_2$ 會在線段$\overline{P_3P_4}$ 的異側。用方向函數表示：$dir(a,b,c)\times dir(a,b,d)<0$。確認完兩個線段之後即完成線段相交的判斷！

```cpp=
bool Intersection(pt a, pt b, pt c, pt d){      //線段ab是否與cd相交
    if(onseg(a,b,c)||onseg(a,b,d))return true;  //點c、d是否洽在線段ab上
    if(onseg(c,d,a)||onseg(c,d,b))return true;  //點a、b是否洽在線段cd上
    if(dir(a,b,c)*dir(a,b,d)==-1 && dir(c,d,a)*dir(c,d,b)==-1)
        return true;                            //對於線段兩端點看另外兩端點必須方向相反
    return false;
}
```

由下圖可以得到上面的結論，當兩線段相交時，方向函數得到的值（用外積，也就是下圖 $\theta_1$ 以及 $\theta_2$）的方向），會呈現一正一負，從兩個相反的方向看同一條線段得出來的結論！
![](https://i.imgur.com/b5pW6IS.png)

AC Code:

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
vector<pt> point;
int dir(pt a, pt b, pt o) {                         //方向函數
    int cross = (a - o) ^ (b - o);
    if(fabs(cross) <= eps) return 0;
    else if(cross > 0) return 1;
    else return -1;
}
bool onseg(pt a, pt b, pt o){                       //o是否在ab線段上
    int cross = (a - o) ^ (b - o);                  //是否平行
    int dot = (a - o) * (b - o);                    //是否在線段中
    return (cross == 0)&&(dot <= 0);
}

bool Intersection(pt a, pt b, pt c, pt d){          //線段ab是否與cd相交
    if(onseg(a,b,c)||onseg(a,b,d))return true;      //點c、d是否洽在線段ab上
    if(onseg(c,d,a)||onseg(c,d,b))return true;      //點a、b是否洽在線段cd上
    if(dir(a,b,c)*dir(a,b,d)==-1 && dir(c,d,a)*dir(c,d,b)==-1)
        return true;                                //對於線段兩端點看另外兩端點必須方向相反
    return false;
}
int n,t;

void solve(){
    point.assign(4,{0,0});
    rep(i,0,3)cin>>point[i].x>>point[i].y;
    if(Intersection(point[0],point[1],point[2],point[3])){
        cout<<"Yes"<<endl;
    }
    else cout<<"No"<<endl;
}

signed main(){
    Orz;
    int t;cin>>t;
    while(t--){
        solve();
    }
}
```
