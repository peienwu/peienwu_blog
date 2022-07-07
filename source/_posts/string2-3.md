---
title: "[題解]NEOJ 267 自動完成系統"
date: 2021-1-9
tags: 
    - 字串
    - 題解
categories: 字串題解
mathjax: true
---

### NEOJ 267 自動完成系統
<!-- more -->
[題目連結](https://neoj.sprout.tw/problem/267/)
[Submission](https://neoj.sprout.tw/challenge/179148/)
> 題目敘述
[點這裡](https://www.facebook.com/codingcompetitions/hacker-cup/2015/round-1/problems/B)有FB Hacker Cup的原題連結，簡單來說就是想像手機的自動填入系統，每加入一個字串會記錄到資料庫中，當資料庫裡面沒有相同前綴的字串時就輸出前綴長度。

用字典樹Trie插入每一個字串，插入過程中返回從頭到開始new新的節點之間經過的節點樹，代表需要輸入多少個字元才能觸發自動完成系統。這一題是基礎的Trie應用。

#### 程式碼

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld long double
#define N 1000005
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int t,n;
char S[N];

int ch(char a){
    return a-'a';
}

struct Trie{
    Trie* c[26];
    Trie(){
        memset(c,0,sizeof(c));
    }
};
Trie *root = new Trie();

int insert(char *S){
    Trie *ptr = root;
    int step = 0,f = 1;
    while(*S){
        if(f)step++;
        if(!ptr->c[ch(*S)]){
            ptr->c[ch(*S)] = new Trie();
            f = 0;
        }
        ptr = ptr->c[ch(*S)];
        S++;
    }
    return step;
}
void clear(Trie *s){
    for(int i=0;i<26;i++){
        if(s->c[i]){
            clear(s->c[i]);
            delete s->c[i];
            s->c[i] = nullptr;
        }
    }
}

int main(){
    cin>>t;
    int cnt = 0;
    while(t--){
        cnt++;
        cin>>n;
        int step = 0;
        rep(i,0,n-1){
            cin>>S;
            step +=insert(S);
        }
        cout<<"Case #"<<cnt<<": "<<step<<endl;
        clear(root);
    }
}
```
