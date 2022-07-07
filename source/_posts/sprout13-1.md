---
title: "[題解]NEOJ 252 Counting Triangles"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### Counting Triangles
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/252/)
我個人認為做法超級精妙的！有很多種作法，挑一個比較好實作的：
{% note success %}
假設可以 $O(1)$ 回答 (x, y) 是否為圖中的一個邊。對於每條邊 (u, v) ，你花$O(min(d_u, d_v))$去算出包含這條邊的三角形個數
總複雜度：$O(M\sqrt{M})$
{% endnote %}

![](https://i.imgur.com/SazG9dP.png)

其中對於均攤查詢一條邊有沒有在圖中，總邊數$O(M)$ $\div$ 詢問每一條邊$O(M)$ = 均攤$O(1)$的複雜度，實作起來非常簡單

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define N 100001
#define M 100001
using namespace std;

int main(){
    ios;
    vector<int> G[N],query[N];  //G是存圖、query是存詢問
    int n,m,x[M],y[M];  //x,y存邊用
    bool visit[N];
    
    cin>>n>>m;
    for(int i=0;i<m;i++){
        cin>>x[i]>>y[i];
        G[x[i]].push_back(y[i]);
        G[y[i]].push_back(x[i]);
    }
    int sml = 0,big = 0;
    for(int i=0;i<m;i++){    //保證複雜度為根號的關鍵
        if(G[x[i]].size()<=G[y[i]].size())sml = x[i];
        else sml = y[i];    //尋找哪一個度數比較小
        big = x[i]+y[i]-sml;
        for(int j : G[sml]){   //訪問跟sml有相鄰的所有邊，看有沒有(j,big)
            query[j].push_back(big);
        }
    }
    int ans = 0;
    for(int i=0;i<n;i++){
        //均攤O(1)?
        for(int j:G[i]){
            visit[j] = true;
        }
        for(int j:query[i]){
            ans+=visit[j];
        }
        for(int j:G[i]){
            visit[j] = false;
        }
    }
    cout<<ans/3<<endl;
}
```
