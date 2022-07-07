---
title: "[題解]NEOJ 59 Heap 練習"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### Heap 練習
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/59/)
刻一個heap嘛，不想刻就call stl( *std::priorityqueue* 就不放上來了)
自己刻一個binary heap ，沒有想象中的簡單，有些細節會不小心漏掉

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define int long long
using namespace std;
int t,top=1,heap[1000005];//complete binary tree

void push(int n){
    heap[top] = n;
    int father_pos = top/2,pos = top;
    while(father_pos>0 && heap[father_pos]>heap[pos]){
        swap(heap[father_pos], heap[pos]);
        pos = father_pos;
        father_pos/=2;
    }
    top = top+1;
}

int pop(){
    int ans = heap[1];
    heap[1] = heap[--top];
    heap[top] = 0;
    int now = 1,left = now*2, right = now*2+1;
    
    int temp = min(heap[right],heap[left]);
    while(heap[left]!=0 && heap[right]!=0 && heap[now]>temp){
        if(heap[now]>heap[right] && heap[left]>heap[right]){
            swap(heap[right], heap[now]);
            now = right;
        }
        else if(heap[now]>heap[left]){
            swap(heap[left], heap[now]);
            now = left;
        }
        right = now*2+1;
        left = now*2;
        temp = min(heap[right],heap[left]);
    }
    if(heap[right]!=0 && heap[now]>heap[right])swap(heap[right], heap[now]);
    else if(heap[left]!=0 && heap[now]>heap[left])swap(heap[left], heap[now]);
    return ans;
}
bool empty(){
    if(heap[1]==0)return 1;
    else return 0;
}
signed main(){
    ios;
    cin>>t;
    memset(heap, 0, sizeof(heap));
    while(t--){
        int a;cin>>a;
        if(a==1){
            int b;cin>>b;
            push(b);
        }
        else if(a==2){
            if(empty())cout<<"empty!"<<endl;
            else cout<<pop()<<endl;
        }
    }
}
```
