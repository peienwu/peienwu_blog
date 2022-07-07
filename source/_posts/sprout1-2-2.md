---
title: "[題解]NEOJ 20 中國人排隊問題"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 中國人排隊問題
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/20/)
這一題有一個條件有點難維護，就是如果同一團體的人要排隊
而隊中已經有同一團人在裡面時，他就要排在團裡面最後一個位置
因此要用iterator 陣列指向每個團隊的末端之類的
好好維護**細節**可以減少debug 的時間！

```cpp=
#include <bits/stdc++.h>
using namespace std;
int n,cnt = 1;

int main(){
    while(cin>>n){
        cout<<"Line #"<<cnt<<endl;
        int arr[1000005],gt[1005];//紀錄在哪一個團體當中
        memset(arr, 0, sizeof(arr));
        memset(gt, 0, sizeof(gt));
        for(int i=1;i<=n;i++){
            int k;cin>>k;
            for(int j=0;j<k;j++){
                int temp;cin>>temp;
                arr[temp] = i;
            }
        }
        list<int> ls;
        list<int>::iterator it[1005];//指向第i個團體的尾端
        while(true){
            string s;int cur;
            cin>>s;
            if(s[0]=='E'){
                cin>>cur;
                int i = arr[cur];
                if(i>0){
                    if(gt[i]==0){
                        ls.push_back(cur);
                        it[i] = --ls.end();
                    }
                    else{
                        it[i] = ls.insert(++it[i],cur);
                    }
                    gt[i]++;
                }
                else ls.push_back(cur);
            }
            else if(s[0]=='D'){
                int i = ls.front();
                if(arr[i]>0)gt[arr[i]]--;
                cout<<ls.front()<<endl;
                ls.pop_front();
            }
            else break;
        }
        cnt++;
    }
}
```
