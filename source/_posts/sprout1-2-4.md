---
title: "[題解]NEOJ 513 超大螢幕設置"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 超大螢幕設置
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/513/)
講師在課堂上有講解這個題目的算法，就是對每個「大樓」（剛剛看才知道）分別往左往右看（單調隊列），看到第一個小於自己就停止
單調隊列問了講師之後，參照他的寫法，用vector 儲存「位置的index」而不要用pair ，明顯增加了程式碼的易讀性！

**這是用pair 版本**：

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0);cin.tie(0);
#define int long long
using namespace std;
int n;

signed main(){
    ios
    cin>>n;
    vector<pair<int,int>> vec;
    for(int i=0;i<n;i++){
        int temp;cin>>temp;
        vec.push_back(make_pair(temp, i+1));
    }
    int ans[n+5][2];
    stack<pair<int, int>> p;
    p.push(vec[0]);
    
    for(int i=1;i<n;i++){
        if(vec[i].first >= p.top().first){
            p.push(vec[i]);
        }
        else{
            while(!p.empty() && p.top().first > vec[i].first){
                ans[p.top().second-1][0] = i-p.top().second+1;
                p.pop();
            }
            p.push(vec[i]);
        }
    }
    while(!p.empty()){
        ans[p.top().second-1][0] = n-p.top().second+1;
        p.pop();
    }
    p.push(vec[n-1]);
    
    for(int i=n-2;i>=0;i--){
        if(vec[i].first >= p.top().first){
            p.push(vec[i]);
        }
        else{
            while(!p.empty() && p.top().first > vec[i].first){
                ans[p.top().second-1][1] = p.top().second-i-1;
                p.pop();
            }
            p.push(vec[i]);
        }
    }
    while(!p.empty()){
        ans[p.top().second-1][1] = p.top().second;
        p.pop();
    }
    
    int total = 0;
    for(int i=0;i<n;i++){
        int sum =0;
        sum = vec[i].first*(ans[i][0]+ans[i][1]-1);
        total = max(total,sum);
    }
    cout<<total<<endl;
}
```

而這個是用講師習慣的作法，明顯短了很多：

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0);cin.tie(0);
#define int long long
using namespace std;
int n;

signed main(){
    ios
    cin>>n;
    int arr[n+5],lft[n+5],rht[n+5];
    memset(arr,0,sizeof(arr));
    
    for(int i=1;i<=n;i++)cin>>arr[i];
    vector<int> stk(1,0);
    
    for(int i=1;i<=n;i++){
        while(arr[i]<=arr[stk.back()])stk.pop_back();
        lft[i] = i-stk.back();
        stk.push_back(i);
    }
    while(!stk.empty())stk.pop_back();
    
    stk.push_back(n+1);
    
    for(int i=n;i>=1;i--){
        while(arr[i]<=arr[stk.back()])stk.pop_back();
        rht[i] = stk.back()-i-1;
        stk.push_back(i);
    }
    int ans[n+5];
    for(int i=1;i<=n;i++){
        ans[i-1] = arr[i]*(lft[i]+rht[i]);
    }
    cout<<*max_element(ans,ans+n)<<endl;
}
```
