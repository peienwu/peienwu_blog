---
title: "[題解]NEOJ 63 Lotto(UVA)"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### Lotto(UVA)
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/63/)
這一題主要就是建立一個排列樹，把每一種可能的情況用遞迴列出來
![](https://i.imgur.com/cKGOtgP.png)
這一題用DFS明顯比BFS來得好、**空間**省很多（用一下資芽的圖XD)
![](https://i.imgur.com/sOjMiuk.png)

來看程式碼吧！

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n,arr[15];

void permutation(int arr_ind, int cur[],int cur_ind){
    if(arr_ind>n)return;
    if(cur_ind>=6){
        for(int i=0;i<6;i++){
            if(i>0)cout<<" ";
            cout<<cur[i];
        }
        cout<<endl;
        return;
    }
    for(int i = 0; i <= n-(6-cur_ind); i++){
        cur[cur_ind] = arr[arr_ind+i];
        permutation(arr_ind+i+1, cur, cur_ind+1);
    }
}

bool f=0;

int main(){
    ios;
    while(cin>>n){
        if(n==0)break;
    if(f)cout<<endl;
    memset(arr, 0, sizeof(arr));
    for(int i=0;i<n;i++)cin>>arr[i];
    sort(arr, arr+n);
    int cur[10];
    permutation(0, cur, 0);
    f=1;
    }
}
```

{% note warning %}
這一題必須很小心輸入輸出，它們會讓你WA很久呀！

1. 行末不要多餘空白
2. 測資全部印出來後，末端**不要多餘換行**

大概就這兩點
{% endnote %}
