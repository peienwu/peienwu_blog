---
title: "[題解]NEOJ 78 包裝禮物"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 包裝禮物
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/78/)
一直吃WA ，吃到懷疑人生哎～
想法：由大方塊到小方塊，盡量填滿，如果過程中大小為4的方塊變成負的，可以先欠著，直到最後一次由大小為1的來補，這樣可以確保每一格方塊不是被1*1的填滿就是剩下最少的空格

```cpp=
#include <bits/stdc++.h>
#define int long long
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int a,b,c,d,e,f;

signed main(){
    ios;
    while(cin>>a>>b>>c>>d>>e>>f){
        if(a==0&&b==0&&c==0&&d==0&&e==0&&f==0)break;
        int ans = f+e+d;
        a = a - 11*e;//5*5可以用11個1*1填
        b = b - 5*d;//一個4*4可以用5個2*2填
        ans+=(c/4);
        if(c%4!=0)ans++;
        if(c%4==1){
            a = a - 7;
            b = b - 5;
        }
        else if(c%4==2){
            a = a - 6;
            b = b - 3;
        }
        else if(c%4==3){
            a = a - 5;
            b = b - 1;
        }
        if(b>0){
            ans+=(b/9);
            if(b%9!=0)ans++;
            a = a-(36-4*(b%9));
        }
        else if(b<0)a = a-(4*(-b));//如果b有欠，可以用a來補
        if(a>0){
            ans+=a/36;
            if(a%36!=0)ans++;
        }
        cout<<ans<<endl;
    }
}
```
