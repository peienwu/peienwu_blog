---
title: "[題解]NEOJ 80 RMQ練習"
date: 2021-1-8
tags: 
    - 線段樹
    - 題解
categories: 線段樹題解
mathjax: true
---

### RMQ練習
<!-- more -->
[題目連結](https://neoj.sprout.tw/problem/80/)
RMQ = Range minimun query，也就是詢問一個區間的最小值。分析一下兩種不同作法的複雜度：
{% note default %}
對於數列 $a_n$ 共n項，k筆詢問，每一次詢問區間$[l,r]$的最大、最小值

**1. 暴力做**
時間複雜度：<font color="#f00">詢問$O(n)$</font>
對於每一筆詢問都暴力搜索，最多掃過n個數字，時間複雜度$O(kn)$，對於數字大一點的情況就會TLE
<br>

**2. 分塊算法**
可以參見 [這篇文章](https://hackmd.io/@peienwu/rkCDKEcjd)

**3. 線段樹**
時間複雜度：<font color="#f00">預處理 $O(n)$、詢問$O(logn)$</font>
首先是預處理建立線段樹，線段樹上約有$2n$個節點，因此空間複雜度是$O(n)$，也就表示預處理是$O(n)$，之後便可$O(logn)$查詢每一筆詢問。以下是各種操作複雜度：

- **初始建構**：所有節點恰會建構一次，每個節點 $O(1)$，配合節點樹可得為 $O(𝑛)$
- **單點修改**：該點的所有祖先節點都會被修改到，其他都不會被修改到，$𝑂(logn)$
- **區間查詢**：每筆詢問最多詢問到深度為 $O(logn)$ 的節點。在一次詢問中，每一層不會有超過2個節點被詢問，總複雜度為$𝑂(log𝑛)$
<br>

**4. 稀疏表(Sparse Table)**
時間複雜度：<font color="#f00">預處理 $O(nlogn)$、詢問$O(1)$</font>
參考 [這篇文章](https://hackmd.io/@peienwu/H1W1Y18Gt)
{% endnote %}

這一題就是基礎的要有支援區間查詢、單點修改的線段樹，也是最簡單的一種！

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0);
#define N 1000000
#define INF 1e9
using namespace std;
int seg[4 * N],arr[N],n,m;

void build(int l,int r,int cur){
    if(r<=l)return;
    if(r-l==1){
        seg[cur] = arr[l];
        return;
    }
    int m = (l+r)/2;
    build(l,m,2*cur);
    build(m,r,2*cur+1);
    seg[cur] = min(seg[2*cur],seg[2*cur+1]);
}

//單點修改
void modify(int cur,int l,int r,int ind,int val){
    if(r<=l)return;
    if(r-l==1 && l==ind){
        seg[cur]=val;
        return;
    }
    int mid = (l+r)/2;
    if(ind<mid)modify(cur*2,l,mid,ind,val);
    else modify(cur*2+1,mid,r,ind,val);
    seg[cur] = min(seg[2*cur],seg[2*cur+1]);
}

//區間詢問
int query(int cur,int l,int r,int ql,int qr){
    if(r<=l || ql>=r || qr<=l)return INF;
    if(ql<=l && qr>=r){
        return seg[cur];
    }
    int mid = (l+r)/2;
    return min(query(cur*2,l,mid,ql,qr),query(cur*2+1,mid,r,ql,qr));
}

int main(){
    ios;
    cin>>m>>n;
    for(int i=1;i<=n;i++)cin>>arr[i];
    build(1,n+1,1);
    
    while(m--){
        int p;cin>>p;
        if(p==2){
            int x,y;cin>>x>>y;
            modify(1,1,n+1,x+1,y);
        }
        else if(p==1){
            int x,y;cin>>x>>y;
            int ans = query(1,1,n+1,x+1,y+2);
            cout<<ans<<endl;
        }
    }
}
```
