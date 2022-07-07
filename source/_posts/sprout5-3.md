---
title: "[題解]TIOJ 1072 誰先晚餐 "
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---


### 誰先晚餐
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/89/)
按照Greegy 的想法，吃最慢的人要最先吃，吃最快的人要後吃，才能讓整個時間盡量的縮短
這一題之前學長講解過，所以自然是比較簡單。But! 要怎麼證明這樣可以得到最佳解呢～留到手寫作業！

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n;

struct student{
    int eat,cook;
}people[10005];

bool comp(student a,student b){
    return a.eat > b.eat;
}
int main(){
    ios;
    while(cin>>n){
        if(n==0)break;
        for(int i=0;i<n;i++){
            cin>>people[i].cook>>people[i].eat;
        }
        sort(people, people+n, comp);
        int ans = 0,cook = 0;
        for(int i=0;i<n;i++){
            cook+=people[i].cook;//煮飯時間一定總數不會改變
            int leave = cook+people[i].eat;
            ans = max(ans, leave);
        }
        cout<<ans<<endl;
    }
}
```
