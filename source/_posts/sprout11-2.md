---
title: "[題解]NEOJ 179 謠言問題"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 謠言問題
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/179)
跟上一題（高棕櫚傳遞鏈）蠻類似的，一樣找到割點，不同的是要在DFS過程中同時紀錄子樹節點的數量。如果碰到割點，維護拔掉它之後分裂出去那些子樹的節點個數，到時候透過節點總數-拔掉後分裂個數即可推算有幾個人會知道謠言，取min即可

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0);
#define int long long
#define N 30001
using namespace std;
int n,m,root,lv[N],low[N],tree_cnt[N],es = 1;
bool visit[N],ans[N];
vector<int> edge[N];

int DFS(int now,int father){      //回傳當前子節點個數
    visit[now] = 1;
    lv[now] = es;
    low[now] = es++;
    
    int len = edge[now].size(),sum=1;//計算節點數
    for(int i=0;i<len;i++){
        int next = edge[now][i];     //下一個節點
        
        if(!visit[next]){
            int temp = DFS(next,now);
            sum += temp;
            if(low[next] >= lv[now] && now!=root){ //不能拔掉root
                ans[now] = 1;           //設為AP
                tree_cnt[now] += temp;  //被拔掉後可被分割成幾個連通塊節點數
            }
        }
        if(next!=father)low[now] = min(low[now],low[next]);
    }
    return sum;
}

signed main(){
    ios;
    cin>>n>>m;
    for(int i=0;i<m;i++){
        int x,y;cin>>x>>y;
        edge[x].push_back(y);
        edge[y].push_back(x);
    }
    cin>>root;
    
    memset(ans, 0, sizeof(ans));
    memset(visit, 0, sizeof(visit));
    memset(tree_cnt, 0, sizeof(tree_cnt));
    
    int sum = DFS(root,root),min_cnt = INT_MAX,min_pos = 0;
    
    for(int i=1;i<=n;i++){
        int temp = sum-tree_cnt[i]; //拔掉後剩下連通塊大小（跟root連的）
        if(ans[i] && temp < min_cnt){
            min_pos = i;        //拔掉第幾個
            min_cnt = temp;     //更新拔掉後剩下連通塊大小
        }
    }
    if(min_cnt == INT_MAX)cout<<0<<endl;
    else cout<<min_pos<<" "<<min_cnt<<endl;
}

```
