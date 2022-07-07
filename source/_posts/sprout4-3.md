---
title: "[題解]NEOJ 69 田忌賽馬(NPSC)"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 田忌賽馬(NPSC)
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/69/)
之前有寫過這一題，不過現在再寫一次還是WA了很久，對於二分搜的維護（到底是取到0還是1）還要更熟悉
除此之外，我因為邊界問題（$10^8$ 或是$10^8+1$）沒有仔細注意就一直WA

```cpp=
#include <bits/stdc++.h>
#define N 10000 
using namespace std;  
lli n,k,t,M,upper = 100000000,lower = -1;  
lli mine[N],develop[N],enemy[N],curr[N];  

bool win(lli day){  
    int it = 0,sum = 0;  
    for(int i=0;i<n;i++)  
        curr[i] = mine[i]+day*develop[i];  
    sort(curr, curr+n);  
    for(int i=0;i<n;i++){  
        if(curr[i]>enemy[it]){  
            sum++;  
            it++;  
        }  
    }  
    return (sum>=k);  
}  
  
int main(){  
    ios::sync_with_stdio(false);  
    cin.tie(0);  
    cin>>t;  
    while(t--){  
        upper = 100000000;lower = -1;  
        cin>>n>>k;  
        for(int i=0;i<n;i++){  
            cin>>mine[i]>>develop[i];  
        }  
        for(int i=0;i<n;i++){  
            cin>>enemy[i];  
        }  
        sort(enemy, enemy+n);  
        if(!win(upper))cout<<-1<<endl;  
        else{  
            while(upper-lower!=1){  
                M = (upper+lower)/2;  
                if(win(M))upper = M;  
                else lower = M;  
            }  
            cout<<upper<<endl;  
        }  
    }  
}  
```

{% note success %}
這樣維護二分搜也可以，就是保證 r 一定是1（l就不一定）

```cpp=
while(r>l){
    int mid = (l+r)/2, arr[n+5];
    for(int i=0;i<n;i++)arr[i] = mine[i]+mid*rate[i];
        sort(arr, arr+n,greater<int>());
        int f = can_win(arr, enemy);
        if(f)r = mid;
        else l = mid+1;
    }
```

單純二分搜，找01分界點！
{% endnote %}
