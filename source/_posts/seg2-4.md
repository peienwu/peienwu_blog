---
title: "[題解]NEOJ 367 RMQ˙改"
date: 2021-1-8
tags: 
    - 線段樹
    - 題解
categories: 線段樹題解
mathjax: true
---

### RMQ˙改
<!-- more -->
[題目連結](https://neoj.sprout.tw/problem/367/)
這一題是Range Maximum Query搭配區間加值，如果直接套用前面RMQ或是區間和的模板就輕鬆許多。這一題主要跟前面題目的不同在於它的sz要維護成1，有別於求區間和要乘上區間大小，每一個值不需要乘上區間大小，因此設成0。

這題第二筆測資明顯就是要卡暴力解法。
![](https://i.imgur.com/tC2Wucw.png)

```cpp=
#include <iostream>
#include <string.h>
#include <stdio.h>
#define ios ios::sync_with_stdio(0),cin.tie(0);
#define N 100005
#define int long long
using namespace std;
const int INF = 1e14;

struct Node{            //線段樹每一個節點
    int val=0,tag=0,sz=0; //val原本的值、tag懶標、sz區間大小
    int rv(){           //回傳實際節點的值
        return val+tag*sz;
    }
}seg[4*N];

int arr[N],n,m;

void build(int l,int r,int cur){    //建立線段樹
    seg[cur].sz = 1;                //更新節點的大小
    if(r<=l)return;                 //空區間回傳
    if(r-l==1){                     //設定當前節點的值
        seg[cur].val = arr[l];
        return;
    }
    int m = (l+r)/2;                //分別遞迴建立左右子樹
    build(l,m,2*cur);
    build(m,r,2*cur+1);
    seg[cur].val = max(seg[2*cur].val,seg[2*cur+1].val);
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
    seg[cur].val = max(seg[2*cur].rv(),seg[2*cur+1].rv());
}

//區間詢問
int query(int cur,int l,int r,int ql,int qr){
    if(r<=l || ql>=r || qr<=l)return -INF;      //空集合直接回傳
    if(ql<=l && qr>=r)return seg[cur].rv();     //包含在要詢問的範圍中，回傳實際值
    push(cur);                                  //將懶標在遞迴下去的過程中下推
    int mid = (l+r)/2;                          //遞迴查詢左右子樹
    return max(query(cur*2,l,mid,ql,qr),query(cur*2+1,mid,r,ql,qr));
}

signed main(){
    ios;
    cin>>n>>m;
    for(int i=1;i<=n;i++)cin>>arr[i];
    build(1,n+1,1);
    
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
