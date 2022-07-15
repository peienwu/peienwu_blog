---
title: "[題解]最小花費的航空之旅"
date: 2021-1-7
tags: 
    - 最短路徑
    - 題解
categories: 最短路徑題解
mathjax: true
---


### 最小花費的航空之旅
<!-- more -->
> 題目敘述：
> 給定很多種連接城市間的聯票，要求從起始站搭乘，可以在中途任意站下車，但下車以後就不能再次上車。輸入的第一行為一個正整數 n (1 ≤ n ≤ 20)，即聯票的種類數。以下 n 行每航為一個聯票的資訊，其中第一個整數為聯票的價格，然後是聯票上城市的數目。
>
> 接下來為一個行程單的資訊，其中第一個正整數為行程單上的城市數目k（包括起始城市，2 ≤ k ≤ 10），以及這些城市的編號（按順序列出）。
>
> 輸出最小花費和對應的方案的其中一組解。

這一題是APCS Class的其中一題，難度頗高，因為他要找最短的路徑，同時增加了可以在任意站下車的條件。首先我們要維護每一個聯票的資訊，它的價值、起點、編號以及經過的城市。接著，我們考慮所有從起點出發的聯票，將每一種聯票上的每一個經過的城市都加入queue中。

跟上一題一樣，當我們每一次都從cost最小的點下手，在轉移的過程中如果將所有的點都走訪一遍，則他的路徑必定是最短的，因為每一次的更新都是從花費最小的路徑去轉移。

{% note primary %}
**使用自定義比較函數放入Priority_queue中**

Priority_queue中的比較函數需要使用到struct或class包覆的比較函數，若要回傳cost的最小值，則必須以大於來寫（有點像預設是pop出最大值，使用的卻是less<int>的比較函數）

```cpp=
struct cmp{
    bool operator()(node a,node b){
        return a.cost > b.cost;
    }
};
```

{% endnote %}

以下是AC Code：

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define double long double
#define N 105
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define rrep(i,l,r) for(int i=l;i<r;i++)
#define pii pair<int,int>
#define pif pair<int,float>
#define x first
#define y second
using namespace std;
int n,k,visit[20];

struct Ticket{
    int id,cost;
    vector<int> next_city;
    //陣列ticket[i]存以i為起點
};
vector<Ticket> ticket[30];          //ticket[i] 起點為i的聯票

struct node{
    int cost,cur_pos,visit_pos;
    vector<int> used_ticket;
    //cur_pos現在所在位置、visit_pos在行程單上位置
};

struct cmp{
    bool operator()(node a,node b){
        return a.cost > b.cost;
    }
    //讓priority_queue可以pop出最小的cost的路徑
};
priority_queue<node,vector<node>,cmp> pq;

signed main(){
    Orz;
    cin>>n;
    rep(i,1,n){
        int cost,num,s;cin>>cost>>num>>s;
        vector<int> temp;
        rep(j,1,num-1){
            int k;cin>>k;
            temp.push_back(k);
        }
        ticket[s].push_back({i,cost,temp});
    }
    cin>>k;
    rep(i,1,k)cin>>visit[i];
    
    for(auto i : ticket[visit[1]]){         //行程第一個起點的聯票起點
        int p = 1;
        for(auto j : i.next_city){
            if(p < k && j == visit[p+1])p++;
            pq.push({i.cost,j,p,{i.id}});
//            cout<<"一開始聯票："<<i.id<<" "<<j<<endl;
        }
    }
    while(!pq.empty()){
        node cur = pq.top();
        pq.pop();
        if(cur.visit_pos == k){
            cout<<"Cost = "<<cur.cost<<", Tickets used: "
                <<cur.used_ticket[0];
            for(int i=1;i<cur.used_ticket.size();i++)
                cout<<", "<<cur.used_ticket[i];
            cout<<endl;
            break;
        }
        for(auto i : ticket[cur.cur_pos]){
            vector<int> vec(cur.used_ticket);
            vec.push_back(i.id);                //將現在使用的第i聯票推入
            int p = cur.visit_pos;
            for(auto j : i.next_city){
                if(p < k && j == visit[p+1])p++;
                pq.push({cur.cost+i.cost,j,p,vec});
            }
        }
    }
}
```
