---
title: "[題解]NEOJ 60 極速馬拉松"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 極速馬拉松
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/60)
這一題難的主要是變數太多
只要理解它到底要幹嘛，就可以發現可以利用$O(N)$枚舉出固定去間內最大的距離
之後再$O(logN)$ 二分搜時間
就可以用$O(logN)$ AC這一題

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0);
#define int long long
using namespace std;
int a,b,c,d,m,s,t;

int func(int time){
    int speed_time = m/c,max_distance = 0,cur_m = m%c;
    int cur_distance = b*speed_time,cur_t = speed_time;

    if(cur_t<time){
        int len = time-cur_t;
        for(int i=0;i<=len;i++){//時限內最多可以跑多遠
            cur_distance = b*speed_time;
            cur_t = speed_time+i;
            cur_m = (m%c)+d*i;

            int step = min(cur_m/c,time-cur_t);

            cur_m = cur_m-step*c;
            cur_t+=step;
            cur_distance += b*step;

            cur_distance+=a*(time-cur_t);
            max_distance = max(max_distance,cur_distance);
        }
    }
    else max_distance = b*time;
    return max_distance;
}

signed main(){
    ios;
    cin>>a>>b>>c>>d>>m>>s>>t;
    int max_distance = func(t);
    if(s>=max_distance)cout<<"No"<<endl<<max_distance<<endl;
    else{
        cout<<"Yes"<<endl;
        int r = t,l = 0;
        while(r-l>1){
            int mid = (r+l)/2;
            if(func(mid)>s)r = mid;
            else l = mid;
        }
        cout<<r<<endl;
    }
}
```
