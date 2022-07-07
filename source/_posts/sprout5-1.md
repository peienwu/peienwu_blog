---
title: "[題解]NEOJ 70 Add all(UVA)"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### Add all(UVA)
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/70/)
這一題可以輕鬆觀察出規律：數字越早被相加，被加總的次數就會越多
因此，維護一個priority_queue，每次pop出最小的兩個數字再push 回去，可以保證總和一定會是最小的

```cpp=
#include <bits/stdc++.h>
#define int long long
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n;

signed main(){
    ios;
    priority_queue<int, vector<int>, greater<int> >qq;
    while(cin>>n && n!=0){
        for(int i=0;i<n;i++){
            int temp;cin>>temp;
            qq.push(temp);
        }
        int ans = 0;
        while(qq.size()>1){
            int a,b;
            a = qq.top();qq.pop();
            b = qq.top();qq.pop();
            qq.push(a+b);
            ans+=(a+b);
        }
        cout<<ans<<endl;
        qq.pop();
    }
}

```
