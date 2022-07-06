---
title: "[題解]ZJ d799 區間求和"
date: 2021-1-8
tags: 
    - 線段樹
    - 題解
categories: 線段樹題解
mathjax: true
---

### d799區間求和
<!-- more -->
[題目連結](https://zerojudge.tw/ShowProblem?problemid=d799)
這一題要求兩個操作，區間加值跟區間查詢，這時候就必須用到**懶標(lazy tags)** 輔助求和。

> 範例測資：
> 10
> 1 2 3 4 5 6 7 8 9 10
> 3
> 2 2 4
> 1 3 6 3
> 2 2 4

![](https://i.imgur.com/MJMDQE9.jpg)
左邊是整棵線段樹，右邊則是懶標，可以看到3要往下推，但學長的程式碼中並沒有執行這個步驟，這樣會導致query的時候發生錯誤。

```cpp=
#include <iostream>
#include <string.h>
#include <stdio.h>
#define ios ios::sync_with_stdio(0),cin.tie(0);
#define N 500005
#define int long long
using namespace std;

struct Node{            //線段樹每一個節點
    int val=0,tag=0,sz; //val原本的值、tag懶標、sz區間大小
    int rv(){           //回傳實際節點的值
        return val+tag*sz;
    }
}seg[4*N];

int arr[N],n,m;

void build(int l,int r,int cur){    //建立線段樹
    seg[cur].sz = r-l;              //更新節點的大小
    if(r<=l)return;                 //空區間回傳
    if(r-l==1){                     //設定當前節點的值
        seg[cur].val = arr[l];
        return;
    }
    int m = (l+r)/2;                //分別遞迴建立左右子樹
    build(l,m,2*cur);
    build(m,r,2*cur+1);
    seg[cur].val = seg[2*cur].val+seg[2*cur+1].val;
}

//把id的懶標在query的時候往下推
void push(int id){
    seg[2*id].tag += seg[id].tag;   //左子樹懶標更新
    seg[2*id+1].tag += seg[id].tag; //右子樹懶標更新
    seg[id].val = seg[id].rv();     //更新實際值
    seg[id].tag = 0;                //往下推完之後設定為預設
}

//區間修改
void modify(int cur,int l,int r,int ql,int qr,int val){
    if (r<=l||ql>=r||qr<=l)return;
    if (ql<=l && qr>=r) {
        seg[cur].tag += val;               //將被完整包含的區間的懶標加上修改值
        return;
    }
    int mid = (l+r)/2;
    modify(cur*2,l,mid,ql,qr,val);          //修改左右子樹
    modify(cur*2+1,mid,r,ql,qr,val);        //遞迴完拉上來的過程中把上面的數值更新成有懶標
    seg[cur].val = seg[2*cur].rv()+seg[2*cur+1].rv();
}

//區間詢問
int query(int cur,int l,int r,int ql,int qr){
    if(r<=l || ql>=r || qr<=l)return 0;         //空集合直接回傳
    if(ql<=l && qr>=r)return seg[cur].rv();     //包含在要詢問的範圍中，回傳實際值
    push(cur);                                  //將懶標在遞迴下去的過程中下推
    int mid = (l+r)/2;                          //遞迴查詢左右子樹
    return query(cur*2,l,mid,ql,qr)+query(cur*2+1,mid,r,ql,qr);
}

signed main(){
    ios;
    cin>>n;
    for(int i=1;i<=n;i++)cin>>arr[i];
    build(1,n+1,1);
    cin>>m;
    
    while(m--){
        int p;cin>>p;
        if(p==1){
            int x,y,k;cin>>x>>y>>k;
            modify(1,1,n+1,x,y+1,k);
        }
        else if(p==2){
            int x,y;cin>>x>>y;
            int ans = query(1,1,n+1,x,y+1);
            cout<<ans<<endl;
        }
    }
}
```
