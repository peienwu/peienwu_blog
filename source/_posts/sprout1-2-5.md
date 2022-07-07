---
title: "[題解]NEOJ 22 檸檬汽水傳說（NPSC）"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 檸檬汽水傳說（NPSC）
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/22/)
這題我覺得很難！
一開始想說維護一個遞增的單調隊列，沒想到一直吃WA
好吧，上網找答案
結果是發現，題目很機車的需要處理相同元素的情況
因為一排一樣的數字可以橫跨很多個數字，形成數對
好不容易處理完之後上傳code 又吃WA
debug 了好久，才發現是因為第一個連續出現的數字好好處理
因為第一個數字就算跟後面連續的數字相同也不能再跟前面構成任何數對！
總算，經過好久好久，終於才AC ，真是得來不易！

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define int long long
using namespace std;
int n,t;

signed main(){
    ios;
    cin>>t;
    while(t--){
        cin>>n;
        vector<int> s;
        
        int ans = 0,cnt[1000005];
        memset(cnt, 0, sizeof(cnt));//紀錄s當下該數出現次數
        while(n--){
            int k,top = s.size();cin>>k;
            
            while(!s.empty() && s.back()<k){
                cnt[top--] = 0;
                s.pop_back();
                ans++;
            }
            if(s.empty())s.push_back(k);
            else{
                if(s.back()==k && top>1){
                    cnt[top+1]+=cnt[top]+1;
                    ans+=cnt[top+1];
                }
                s.push_back(k);ans++;
            }
        }
        cout<<ans<<endl;
    }
}
```
