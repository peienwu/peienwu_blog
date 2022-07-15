---
title: "[題解]Codeforces 543B: Destroying Roads"
date: 2021-1-7
tags: 
    - 最短路徑
    - 題解
categories: 最短路徑題解
mathjax: true
---

### Codeforces 543B: Destroying Roads
<!-- more -->
[題目連結](https://codeforces.com/problemset/problem/543/B)
[Submission](https://codeforces.com/problemset/submission/543/125743532)

> 題目大意
> 給定N個點M條無向邊，每一邊權重都是1，以及兩筆資料，由三個數$(x,y,w)$ 構成，代表起點為x、終點為x、要在w距離內從起點走到終點。試問最多可以拔掉幾條邊同時滿足以上兩個資料所描述的特性？

這一題蠻有趣的，首先他的邊權重都是1，因此我們可以直接用BFS尋找最短路徑，並且點第一次拜訪到時的就是該點的最短距離（BFS一層一層擴展）。

{% note primary %}
**第一個想法：錯誤想法**
我先將兩個起點與終點的最短路徑都找出來，把將過的邊都標上不能移除，將其他的邊全部拔掉。

**問題點：**
固然找到的是最短路徑，但不代表可以最大化拔除邊的數量。因為題目要求兩點只要符合最短距離 $w$ 即可，因此每一個配對其實不用符合是最短路徑的情況（如下圖）！目標是在題目要求的限度內最大化重複的邊，使能夠被拔除的邊最大化！

![](https://i.imgur.com/QfbfF82.png)

上圖紅色線段是$\delta(1,7)$的最短路徑、褐色是線段$\delta(3,6)$的最短路徑、黑色線段是皆以最短路徑之下可以被拔除的邊。但是如果將路徑$\delta(3,6)$換成是$(3,2)\to(2,4)\to(4,5)\to(5,6)$，被拔除的邊可以增過為三條。
{% endnote %}

這一題最重要的關鍵就是以$O(V^2)$枚舉所有點對（距離長度可以直接換算成邊的數量），可以先用$O(V(V+E)$的時間對每一個點用進行BFS，如此一來總複雜度即為$O(V^2+VE)$，所幸題目給定總共的邊數不會超過3000條，因此是可以在時間限制內完成枚舉。

{% note primary %}
枚舉路徑 $\delta(i,j)$ 為共同邊的時候，必須考慮起點與終點的方向，同時考慮從 $i\to j$ 以及從 $j\to i$ 兩個方向。以下圖為例，當枚舉都使用相同的起點以及終點，會讓下圖的 $(3,4)$ 被重複計算！

![](https://i.imgur.com/tjq8oe7.png)
{% endnote %}

以下是AC程式碼：

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define N 3005
#define FOR(i,n) for(int i=0;i<n;i++)
#define pii pair<int,int>
#define pid pair<int,double>
#define pdi pair<double,int>
using namespace std;
int n,m,s1,e1,w1,s2,e2,w2,dis[N][N];
bool visit[N];
vector<int> edge[N];

int main(){
    cin>>n>>m;
    for(int i=0;i<m;i++){
        int a,b;cin>>a>>b;
        edge[a].push_back(b);
        edge[b].push_back(a);
    }
    cin>>s1>>e1>>w1>>s2>>e2>>w2;
    memset(dis,0x3f3f3f3f,sizeof(dis));
    
    for(int i=1;i<=n;i++){
        memset(visit,0,sizeof(visit));
        queue<int>que;
        que.push(i);
        visit[i] = 1;
        dis[i][i] = 0;
        while(!que.empty()){
            int cur = que.front();
            que.pop();
            for(auto j:edge[cur]){
                if(visit[j])continue;
                dis[i][j] = dis[i][cur]+1;
                visit[j] = 1;
                que.push(j);
            }
        }
    }
    if(dis[s1][e1]>w1||dis[s2][e2]>w2){
        cout<<-1<<endl;
        return 0;
    }
    int ans = dis[s1][e1]+dis[s2][e2];

    //O(n^2)枚舉所有共同邊
    for(int i=1;i<=n;i++){
        for(int j=1;j<=n;j++){
            if(dis[s1][i]+dis[i][j]+dis[j][e1]<=w1&&dis[s2][i]+dis[i][j]+dis[j][e2]<=w2)
                ans = min(ans,dis[s1][i]+dis[i][j]+dis[j][e1]+dis[s2][i]+dis[j][e2]);
            if(dis[s1][i]+dis[i][j]+dis[j][e1]<=w1&&dis[e2][i]+dis[i][j]+dis[j][s2]<=w2)
                ans=min(ans,dis[s1][i]+dis[i][j]+dis[j][e1]+dis[e2][i]+dis[j][s2]);
            
        }
    }
    cout<<m-ans<<endl;
}
```
