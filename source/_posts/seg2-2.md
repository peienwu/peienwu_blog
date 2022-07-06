---
title: "[題解]NEOJ 249 最大連續和？"
date: 2021-1-8
tags: 
    - 線段樹
    - 題解
categories: 線段樹題解
mathjax: true
---

### 最大連續和？
<!-- more -->
[題目連結](https://neoj.sprout.tw/problem/249/)
這題題結果我是WA在INF太大可能會溢位的問題，把INF調整成 $5\times 10^{14}$ 是差不多的

這一題要維護區間最大連續和，跟dp的最大連續和差了「區間」兩字，整個實作的方式跟複雜度就不一樣了。DP的作法如果直接套用在這一題的話，複雜度約為：$O(QN)$，絕對會炸裂，因此只能用線段樹維護這個東西。

{% note primary %}
**區間最大連續和**
在分治法的單元有提到分治法最重要的是假定左右兩側（對應過來就是兩子樹）是維護好的，也就是符合定義，要想的是要如何處理橫跨左右子樹的情況。
要如何知道橫跨中間的最大值呢？這時候我們可以維護4個數值，來更新每一個線段樹上的節點的數值。

1. **總和(sum)**
$cur.sum = left.sum+right.sum$
3. **最大前綴和(lmax)**
$cur.lmax = max(left.lmax,left.sum+right.lmax)$
5. **最大後綴和(rmax)**
$cur.rmax = max(right.rmax,right.sum+left.rmax)$
7. **區間最大連續和(tmax)**
$cur.tmax = max(left.tmax,right.tmax,left.rmax+right.lmax)$
{% endnote %}
維護最大後綴跟最大前綴和（首跟末有被取到的情況），就可以$O(1)$ 維護每一個節點的區間最大連續和，複雜度：詢問$O(logn)$，因此總時間$O(Qlogn)$就可以AC了!

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0);
#define int long long
#define N 100005
#define INF 500000000000000
using namespace std;
int arr[N],n,m;

struct Node{
    int sum;    //區間總和
    int lmax;   //最大前綴和
    int rmax;   //最大後綴和
    int tmax;   //區間連續最大和
}seg[N<<2];

//求當下的區間連續最大和
void modify(Node &cur,Node &left,Node &right){
    cur.sum = left.sum+right.sum;
    cur.lmax = max(left.lmax,left.sum+right.lmax);
    cur.rmax = max(right.rmax,right.sum+left.rmax);
    cur.tmax = max(max(left.tmax,right.tmax),left.rmax+right.lmax);
}

//建立線段樹：[l,r)
void build(int l,int r,int cur){
    if(r<=l)return;
    if(r-l==1){
        seg[cur] = {arr[l],arr[l],arr[l],arr[l]};
        return;
    }
    int mid = (l+r)/2;
    build(l,mid,2*cur);
    build(mid,r,2*cur+1);
    modify(seg[cur],seg[2*cur],seg[2*cur+1]);
}

//區間詢問：[l,r)
Node query(int cur,int l,int r,int ql,int qr){
    if(r<=l || ql>=r || qr<=l)return {-INF,-INF,-INF,-INF};
    if(ql<=l && qr>=r)return seg[cur];
    int mid = (l+r)/2;
    auto left = query(2*cur,l,mid,ql,qr);
    auto right = query(2*cur+1,mid,r,ql,qr);
    Node temp;
    modify(temp,left,right);
    return temp;
}

signed main(){
    ios;
    cin>>n>>m;
    for(int i=1;i<=n;i++)cin>>arr[i];
    build(1,n+1,1);
    
    while(m--){
        int l,r;cin>>l>>r;
        auto ans = query(1,1,n+1,l,r+1);
        cout<<max(ans.tmax,(long long)0)<<endl;
    }
}
```
