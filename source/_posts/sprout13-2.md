---
title: "[題解]NEOJ 213 中國人插隊問題"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 中國人插隊問題
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/213/)
如果用一個vector 存整個序列，query $O(1)$，但新增跟刪除都是$O(N)$
因此，如果改用分塊的方法，用deque下去砸，每K個分成一塊，對於插入與刪除都是$O(K+\frac{N}{K})$，詢問是$O(1)$
根據算幾不等式，我們可以取$K = \sqrt{N}$ 時，複雜度可以達到$O(\sqrt{N})$ ，很不錯吧！

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;

signed main(){
    ios;
    int n,m,K;
    cin>>n>>m;
    K = sqrt(n);
    deque<int> deq[n/K+(m/K)+10];
    for(int i=0;i<n;i++){
        int temp;cin>>temp;
        deq[i/K].push_back(temp);
    }
    for(int i=0;i<m;i++){
        string a;cin>>a;
        if(a[0]=='A'){
            int x,y;cin>>y>>x;y--;
            deq[y/K].insert(deq[y/K].begin()+(y%K), x);
            int len = deq[y/K].size(),cur = y/K;
            while(len>K){
                int temp = deq[cur].back();
                deq[cur].pop_back();
                cur++;
                deq[cur].push_front(temp);
                len = deq[cur].size();
            }
        }
        else if(a[0]=='L'){
            int x;cin>>x;x--;
            deq[x/K].erase(deq[x/K].begin()+(x%K));
            int len = deq[x/K].size(),cur = x/K;
            while(len<K && deq[cur+1].size()!=0){
                int temp = deq[cur+1].front();
                deq[cur+1].pop_front();
                deq[cur].push_back(temp);
                cur++;
                len = deq[cur].size();
            }
        }
        else if(a[0]=='Q'){
            int x;cin>>x;x--;
            int len = x%K;
            auto it = deq[x/K].begin();
            cout<<*(it+len)<<endl;
        }
    }
}
```
