---
title: "[題解]NEOJ 19 大善人老闆救濟東南亞兒童"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 大善人老闆救濟東南亞兒童
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/19/)
![](https://i.imgur.com/1MHCZeb.png)
這一題很熟悉，在去年9月剛開學的資讀學長有講過，結果那時候根本聽不懂QQ
但現在來看，這一題就是模擬stack ，很基礎的題目
可以用queue (也可以不用)配上stack，分別模擬Station 跟B站 的情況

```cpp=
#include <bits/stdc++.h>
using namespace std;
int t;

signed main(){
    cin>>t;
    while(t--){
        int n,arr[100005];cin>>n;
        stack<int>s;
        queue<int>q;
        for(int i=0;i<n;i++){
            cin>>arr[i];
            q.push(arr[i]);
        }
        
        for(int i=0;i<n;i++){
            s.push(i+1);//push 進station
            while(!s.empty()&&!q.empty()&&s.top()==q.front()){
                s.pop();q.pop();
            }
        }
        if(s.empty()&&q.empty())cout<<"Yes"<<endl;
        else cout<<"No"<<endl;
    }
}
```
