---
title: "[題解]NEOJ 266 溫力的故事"
date: 2021-1-9
tags: 
    - 字串
    - 題解
categories: 字串題解
mathjax: true
---

### NEOJ 266 溫力的故事
<!-- more -->
[題目連結](https://neoj.sprout.tw/problem/266/)
[Submission](https://neoj.sprout.tw/challenge/179031/)
> 題目敘述
> 給你n個字串m筆詢問一個字串，對每一筆詢問輸出詢問在n個字串中出現的次數。

這一題在**隨機算法**做過，今天用字典樹Trie做一次。在隨機算法中，透過Rolling Hash的公式，對每一個字串生成一個值，利用這個值查詢出現的次數。如果我們用Trie的話，則是建立一棵指標樹，透過走法這一棵字典樹得知詢問字串出現的次數！

#### 程式碼

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld long double
#define N 2000
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int n,m;

struct Trie{        //利用指標建立一棵樹
    Trie* c[26];    //對應a-z每一條邊
    int cnt;        //字串出現次數
    Trie(): cnt(0){ //初始設定
        memset(c,0,sizeof(c));
    }
};
Trie* root = new Trie();

int ch(char temp){
    return temp-'a';
}

void insert(char *s){
    Trie *ptr = root;           //從根節點尋找
    while(*s){
        if(!ptr->c[ch(*s)])     //如果樹上無此字元則new
            ptr->c[ch(*s)] = new Trie();
        ptr = ptr->c[ch(*s)];   //繼續造訪Trie
        s += 1;                 //字串下一個字元
    }
    ptr->cnt += 1;              //字串出現次數(字串對應唯一葉節點)
}

int find(char *s){              //查找字串s
    Trie *ptr = root;           //根節點尋找
    while(*s){                  //無此字串，回傳次數0
        if(!ptr->c[ch(*s)])return 0;
        ptr = ptr->c[ch(*s)];
        s += 1;                 //字串下一個字元
    }
    return ptr->cnt;            //回傳字串出現次數
}

signed main(){
    Orz;
    cin>>n>>m;
    rep(i,0,n-1){
        char s[105];cin>>s;
        insert(s);
    }
    rep(i,0,m-1){
        char s[105];cin>>s;
        cout<<find(s)<<endl;
    }
}
```
