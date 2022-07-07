---
title: "[題解]ZJ d518: 文字抄寫 II"
date: 2021-1-9
tags: 
    - 字串
    - 題解
categories: 字串題解
mathjax: true
---
### ZJ d518: 文字抄寫 II
<!-- more -->
[題目連結](https://zerojudge.tw/ShowProblem?problemid=d518)
> 題目敘述
若這個字串之前已經出現過，則輸出的出現號碼，若沒有則輸出它將被編寫的號碼.

裸題Trie，不過要注意每一次要重置，將所有的實體Delete掉。

#### 程式碼

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld long double
#define N 110
#define Orz ios::sync_with_stdio(0),cin.tie(0),cout.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int n,m;

struct Trie{
    Trie* c[26];
    int ind;
    Trie(): ind(0){
        memset(c,0,sizeof(c));
    }
};
Trie* root = new Trie();

int ch(char temp){
    return temp-'a';
}
void clear(Trie *cur){
    for(int i=0;i<26;i++){
        if(cur->c[i]){
            clear(cur->c[i]);
            delete[] cur->c[i];
            cur->c[i] = NULL;
        }
    }
}

void insert(char *s,int num){
    Trie *ptr = root;           //從根節點尋找
    while(*s){
        if(!ptr->c[ch(*s)])     //如果樹上無此字元則new
            ptr->c[ch(*s)] = new Trie();
        ptr = ptr->c[ch(*s)];   //繼續造訪Trie
        s += 1;                 //字串下一個字元
    }
    ptr->ind = num;
}

int find(char *s){              //查找字串s
    Trie *ptr = root;           //根節點尋找
    while(*s){                  //無此字串，回傳次數0
        if(!ptr->c[ch(*s)])return 0;
        ptr = ptr->c[ch(*s)];
        s += 1;                 //字串下一個字元
    }
    return ptr->ind;
}

signed main(){
    while(scanf("%d",&n)!=EOF){
        int ans,cur_id = 1;
        rep(i,1,n){
            char s[N];scanf("%s",s);
            ans = find(s);
            if(ans == 0){
                printf("New! %d\n",cur_id);
                insert(s,cur_id);
                cur_id += 1;
            }
            else{
                printf("Old! %d\n",ans);
            }
        }
        clear(root);
    }
}
```
