---
title: "[題解]NEOJ 91 調校高棕櫚"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 調校高棕櫚
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/91/)
如果想要讓數字盡量小，那勢必要讓數字的位數盡量少
因此可以先把3個2合併成8，2個3合併成9
而剩下的2跟3可以有6 種組合：

| 數字個數 |0個2 | 1個2 |2個2|
| - | - | - |-|
|0個3|無  | 2 |4|
|1個3|2  | 6 |2,6|

先把數字做質因數分解，如果有質數大於10，就輸出-1
接下來，再用這6種組合用**if** 排列組合就完成了

```cpp=
#include <bits/stdc++.h>
#define int long long
using namespace std;
int t;

signed main(){
    cin>>t;
    while(t--){
        int n,arr[11]={0};cin>>n;
        if(n==1){
            cout<<1<<endl;
            continue;
        }
        int ind = 2;
        while(ind<=9){
            if(n%ind==0){
                n /= ind;
                arr[ind]++;
            }
            else ind++;
        }
        if(n>10){
            cout<<-1<<endl;
            continue;
        }
        vector<int>vec;
        
        for(int i=0;i<arr[2]/3;i++)vec.push_back(8);
        arr[2]=arr[2]%3;

        for(int i=0;i<arr[3]/2;i++)vec.push_back(9);
        arr[3]=arr[3]%2;
        
        if(arr[2]==1 && arr[3]==0)vec.push_back(2);
        if(arr[2]==2 && arr[3]==0)vec.push_back(4);
        if(arr[2]==0 && arr[3]==1)vec.push_back(3);
        if(arr[2]==1 && arr[3]==1)vec.push_back(6);
        if(arr[2]==2 && arr[3]==1){
            vec.push_back(2);
            vec.push_back(6);
        }
        for(int i=0;i<arr[5];i++)vec.push_back(5);
        for(int i=0;i<arr[7];i++)vec.push_back(7);
        sort(vec.begin(),vec.end());
        for(auto i:vec)cout<<i;
        cout<<endl;
    }
}
```
