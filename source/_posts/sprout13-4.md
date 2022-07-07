---
title: "[題解]NEOJ 722 小咲的玩具"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 小咲的玩具
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/722/)
這一題的常數超緊，因此在嘗試使用*hash*時用*unordredmap*被卡*TLE*，在很電的人提示後，要使用黑魔法cc_hash_table才可能會過關

```cpp=
#include <ext/pb_ds/assoc_container.hpp>
using namespace __gnu_pbds;
cc_hash_table<int,int>cc;
```

他的用法其實跟*unordered_map*幾乎一樣吧（至少基本的insert跟find的語法都一樣），不過他可以做到比*unordered_map*更好的效率。不過我用[最近點對](https://neoj.sprout.tw/problem/795/)這一題測試效率卻發現黑魔法會tle，可能是在clear這一個步驟的效率並不是很好吧！

總之，就是對於每一筆詢問(x,y)，不失一般性假設size_y > size_x，則對於y排序並預處理前綴和，同時枚舉x中的所有點並二分搜他在y的位置
最重要的，是對每一筆詢問都儲存起來，這樣複雜度會瞬間少一個N
**補複雜度證明**

```cpp=
#include <bits/stdc++.h>
using namespace std;
#include <ext/pb_ds/assoc_container.hpp>
using namespace __gnu_pbds;
#define int long long
#define N 150006

int n,k,q;
vector<int> vec[N],pre[N];
bool visit[N];
cc_hash_table<int,int>cc;
 
int HASH(int x,int y){
    return x*1000000007+y;
}
signed main(){
    scanf("%lld %lld %lld",&n,&k,&q);
    memset(visit, 0, sizeof(visit));
    while(k--){
        int c,p;scanf("%lld %lld",&c,&p);
        vec[p].push_back(c);
    }
    while(q--){
        int x,y;scanf("%lld %lld",&x,&y);
        if(vec[x].size()>vec[y].size())swap(x, y);
        
        if(cc.find(HASH(x,y))!=cc.end()){
            printf("%lld\n",cc.find(HASH(x,y))->second);
            continue;
        }
        else if(cc.find(HASH(y,x))!=cc.end()){
            printf("%lld\n",cc.find(HASH(y,x))->second);
            continue;
        }
        
        int len = vec[y].size();
        if(!visit[y]){
            sort(vec[y].begin(),vec[y].end());
            pre[y].resize(len+6);
            for(int i=0;i<=len;i++)pre[y][i+1] = pre[y][i]+vec[y][i];
            visit[y] = 1;
        }
        
        int ans = 0;
        for(int j:vec[x]){
            int pos = lower_bound(vec[y].begin(), vec[y].end(),j)-vec[y].begin();
            pos--;
            if(pos<0)ans+=(len*j);
            else ans += (pre[y][pos+1]+j*(len-pos-1));
        }
        printf("%lld\n",ans);
        cc.insert(make_pair(HASH(x,y),ans));
        cc.insert(make_pair(HASH(y,x),ans));
    }
}
```
