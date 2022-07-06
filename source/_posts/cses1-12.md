---
title: "[題解]CSES 1755 Palindrome Reorder"
date: 2021-1-3
tags: 
    - CSES
    - 題解
categories: CSES題解
mathjax: true
---

### Palindrome Reorder
<!--more-->
[題目連結](https://cses.fi/problemset/task/1755)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define N 1000005
using namespace std;
int n;
 
signed main(){
    string str,ans;cin>>str;
    n = str.size();
    int num[N],cnt = 0,cha = -1;memset(num,0,sizeof(num));
    for(int i = 0;i < n;i++){
        num[str[i] - 'A'] += 1;
    }
    for(int i=0;i<26;i++){
        if(num[i] & 1){
            cnt++;cha = i;
        }
    }
    ans.resize(n);
    if(cnt > 1)cout<<"NO SOLUTION\n";
    else if(cnt == 1 && !(n&1))cout<<"NO SOLUTION\n";
    else if(cnt < 1 && n&1)cout<<"NO SOLUTION\n";
    else{
        if(n & 1){
            ans[n/2] = 'A'+cha;
            num[cha]--;
        }
        int l = 0,r = n-1;
        for(int i=0;i<26;i++){
            while(num[i]){
                ans[l++] = i + 'A';
                ans[r--] = i + 'A';
                num[i] -= 2;
            }
        }
        cout<<ans<<"\n";
    }
}
```
