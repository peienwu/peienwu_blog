---
title: "[題解]NEOJ 169 高棕櫚的意外收穫"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 高棕櫚的意外收穫
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/169/)
[講義連結](https://www.csie.ntu.edu.tw/~sprout/algo2021/ppt_pdf/week11/euler_hamilton.pdf)

這一題就是按照字典序print出尤拉路徑，特別用**鄰接矩陣**來存圖是因為要按照字典序輸出，如果用鏈結串列來存還要花時間排序，因此用大一點的空間來節省時間

尤拉路徑也是一筆畫問題，每一個節點一定會有進有出，因此原先的無向圖上每一點一定度數為偶數，若有奇點則選擇其中一個奇點作為起點，當奇點數量超過兩個則代表無解。我們將DFS的過程中離開當前節點的順序紀錄起來，逆序輸出就是一組合法歐拉迴路的解了！
{% note info %}
**實作程序**

**1. 判斷奇點個數**，若奇點個數k：

* k > 2，那麼無解
* k = 2，則選擇其中一個奇點作為起點
* k = 0，則選擇任意一個點作為起點

**2. DFS 執行下列步驟**
> 若當前節點還有尚未走過的邊，那麼拜訪該邊，並在拜訪完後輸出該邊
> 否則離開當前結點

**3. 若還有節點尚未拜訪，則無解**
**4. 否則輸出順序即為一組解**
{% endnote %}

```cpp=
#include <bits/stdc++.h>
#define N 501
using namespace std;
int n,Edge[N][N],ans[1025],ind = 0;

void DFS(int cur){
    for(int i=1;i<=500;i++){
        if(Edge[cur][i]){
            Edge[cur][i]--;Edge[i][cur]--;
            DFS(i);
        }
    }
    ans[ind++] = cur;
}

signed main(){
    cin>>n;
    memset(Edge, 0, sizeof(Edge));
    for(int i=0;i<n;i++){
        int a,b;cin>>a>>b;
        Edge[a][b]++;
        Edge[b][a]++;
    }
    int start = 1;                //開始的節點編號
    for(int i=1;i<=500;i++){
        int sum = 0;
        for(int j=1;j<=500;j++){
            sum+=Edge[i][j];
        }
        if(sum%2!=0){             //找到第一個度數為奇數的節點
            start = i;
            break;
        }
    }
    DFS(start);
    for(int i=ind-1;i>=0;i--)cout<<ans[i]<<endl;
}
```
