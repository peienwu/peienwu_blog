---
title: "[題解]NEOJ 742 第 Z 小"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 第 Z 小
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/742/)
超討厭，一直RE，結果是卡在沒有開long long，哭啊
這一題是值域分塊，一樣是根據值域每K個分成一塊，去維護每一大塊的數字數量總和，這樣在查詢(query)的時候花 $O(C/K)$ 找到相應大塊(C為值域)，再花 $O(K)$ 的時間掃過小塊，而加值減值都是$O(1)$可以處理
複雜度：$O(K+\frac{C}{K})$ 取 K=$\sqrt{C}$ 有最小值：$O(Q\sqrt{C})$

```cpp=
#include <bits/stdc++.h>
#define int long long
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define N 100005
using namespace std;    //值域分塊
const int K = 100;

signed main(){
    ios;
    int q,arr[N],mn[N/K+10];cin>>q;
    memset(arr, 0, sizeof(arr));
    while(q--){
        int temp;cin>>temp;
        if(temp==1){
            int x,y;cin>>x>>y;
            arr[x]+=y;
            mn[x/K]+=y;
        }
        else if(temp==2){
            int x,y;cin>>x>>y;
            arr[x]-=y;
            mn[x/K]-=y;
        }
        else if(temp==3){
            int z,sum = 0,ind=0;cin>>z; //ind查詢位於第幾塊
            while(sum+mn[ind]<z)sum+=mn[ind++];
            int cur_ind = ind*K;
            for(int i=0;i<=K;i++){
                if(arr[cur_ind+i]){
                    sum+=arr[cur_ind+i];
                    if(sum>=z){
                        cout<<ind*K+i<<endl;
                        break;
                    }
                }
            }
        }
    }
}
```
