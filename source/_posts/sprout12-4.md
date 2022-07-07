---
title: "[題解]NEOJ 740 矩陣乘法"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 矩陣乘法
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/740/)
給定三個矩陣ABC，驗證A乘上B是否為C。
當然可以用$O(n^3)$的時間實際驗證A乘上B的結果與C比對，但其實有更快的方法
如下，構造出矩陣R，這一題可以思考要1乘上多少的矩陣，透過兩次O(n^2)的乘法，就可以比對這個R矩陣乘上C矩陣的結果
這題蠻簡單的，一次就過了
![](https://i.imgur.com/LvdbXi8.png)

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define int long long int
#define N 1505
#define mod 1000000007
using namespace std;
int n,m,k,a[N][N],b[N][N],c[N][N],R[N],temp[N],c_new[N];

void init(){
    memset(temp, 0, sizeof(temp));
    srand(time(NULL));
    for(int i=0;i<N;i++){
        int temp = rand()%mod;
        R[i] = temp;
    }
}

void input(){
    cin>>n>>m>>k;
    for(int i=0;i<n;i++){
        for(int j=0;j<m;j++){
            cin>>a[i][j];
        }
    }
    for(int i=0;i<m;i++){
        for(int j=0;j<k;j++){
            cin>>b[i][j];
        }
    }
    for(int i=0;i<n;i++){
        for(int j=0;j<k;j++){
            cin>>c[i][j];
        }
    }
}

signed main(){
    ios;
    init();
    input();
    for(int i=0;i<m;i++){
        for(int j=0;j<k;j++){
            temp[i]+=(b[i][j]*R[j])%mod;
        }
    }
    for(int i=0;i<n;i++){
        for(int j=0;j<m;j++){
            c_new[i]+=(a[i][j]*temp[j])%mod;
        }
    }
    bool flag = 1;
    for(int i=0;i<n;i++){
        int sum = 0;
        for(int j=0;j<k;j++){
            sum+=(c[i][j]*R[j])%mod;
        }
        if(c_new[i]!=sum){
            flag = 0;
            break;
        }
    }
    if(flag)cout<<"Yes"<<endl;
    else cout<<"No"<<endl;
}
```
