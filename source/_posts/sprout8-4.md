---
title: "[題解]NEOJ 421 取名字好困難QQ"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 取名字好困難QQ
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/421/)
跟題目一樣，我覺得要通靈才能想到這一題的作法！
結果是問了別人才大概感受到這一種作法！！！
![](https://i.imgur.com/EmxSHqM.png)

我們既然不知道到底一個數字要不要乘2，我們可以透過做LIS的過程來做決定。當我們把乘與2之後的數字跟原本數字一起push進去，就可以發現到LIS不可能同時取到2個數字。利用這個方法就可以用LIS的過程決定一個數字到底應該要變2倍還是不用。
要找到最長的非嚴格遞增序列，最大的差別就是要把原本的lower_bound改成upper_bound。一整天想一題的感覺超級糟糕QQ

```cpp=
#include <bits/stdc++.h>
#define int long long
#define mod 1000000007
#define FOR(i,n) for(int i=0;i<n;i++)
#define ios ios::sync_with_stdio(0)
using namespace std;
int t;

void solve(){
    int n,m;cin>>n>>m;
    vector<int>vec;
    FOR(i,n){
        int temp;cin>>temp;
        if(2*temp<m)continue;
        else if(temp<m){
            temp*=2;
            vec.push_back(temp);
        }
        else{
            vec.push_back(2*temp);
            vec.push_back(temp);
        }
    }
    if(vec.size()<1){
        cout<<0<<endl;
        return;
    }
    
    //正常做LIS
    vector<int> lis;int len = vec.size();
    lis.push_back(vec[0]);
    for(int i=1;i<len;i++){
        if(lis.back()<=vec[i])lis.push_back(vec[i]);
        else *upper_bound(lis.begin(),lis.end(),vec[i]) = vec[i];
    }
    cout<<lis.size()<<endl;
}

signed main(){
    ios;
    cin>>t;
    while(t--){
        solve();
    }
}

```
