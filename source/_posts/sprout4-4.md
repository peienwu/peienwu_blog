---
title: "[題解]NEOJ 72 Happiness Function"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### Happiness Function(2013 台清交程式設計競賽)
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/72/)
很酷的題目，首先必須做一點數學上的分析
{% note info %}
假設題目給定的n個二次函數依序為$A_1(x),A_2(x),...,A_n(x)$
令函數$f(t)=max(A_j(t), 1<=j<=n)$
則$f(t)$為一個U型函數
{% endnote %}
有了這個性質之後就可以利用**三分搜**來找U型函數的最小值!

值得注意的是：

1. *float* 是**單精度**浮點數、*double* 是**雙精度**浮點數，我用 float吃了好幾個WA，結果用double 直接 AC
2. while 迴圈裡面的判斷不要用浮點數相減，會出事（TLE）

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define float a
using namespace std;
int T,n;
double a[12],b[12],c[12];

double func(double t, int i){
    return a[i]*(t-b[i])*(t-b[i])+c[i];
}

int main(){
    ios;
    cout<<fixed<<setprecision(5);
    cin>>T;
    while(T--){
        cin>>n;
        for(int i=0;i<n;i++)cin>>a[i]>>b[i]>>c[i];
        double left = 0.0, right = 300.0;
        double now_l = 0,now_r = 0,nl = 0,nr = 0;
        int cnt = 0;
        while(cnt<=10000){
            nl = (2*left+right)/3;
            nr = (2*right+left)/3;
            now_l = now_r = 0;
            for(int i=0;i<n;i++)now_r = max(now_r, func(nr,i));
            for(int i=0;i<n;i++)now_l = max(now_l, func(nl,i));
            if(now_r > now_l)right = nr;
            else left = nl;
            cnt++;
        }
        cout<<now_l<<endl;
    }
}
```

簡單統整，就是三分搜找最低點！
