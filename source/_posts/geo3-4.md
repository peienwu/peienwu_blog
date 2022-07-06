---
title: "[題解]Leetcode 587 Erect the Fence"
date: 2021-1-5
tags: 
    - Leetcode
    - 題解
categories: Leetcode題解
mathjax: true
---

### 587 Erect the Fence
<!--more-->
[題目連結](https://leetcode.com/problems/erect-the-fence)

```cpp=
#define pii pair<int,int>
#define ff first
#define ss second

class Solution {
public:
    
    bool check(pii a,pii b,pii o){
        pii aa = {a.ff - o.ff,a.ss - o.ss};
        pii bb = {b.ff - o.ff,b.ss - o.ss};
        int cross = aa.ff * bb.ss - aa.ss * bb.ff;
        return cross > 0;
    }
    
    vector<vector<int>> outerTrees(vector<vector<int>>& point) {
        vector<pii> h;
        int n = point.size();
        vector<pii> p(n);
        for(int i = 0;i < n;i++)p[i] = {point[i][0],point[i][1]};
        sort(p.begin(),p.end());
        for(auto i : p){
            while(h.size() > 1 && check(i,h[h.size()-1],h[h.size()-2]))
                h.pop_back();
            h.push_back(i);
        }
        int down = h.size();
        h.pop_back();
        reverse(p.begin(),p.end());
        for(auto i : p){
            while(h.size() > down && check(i,h[h.size()-1],h[h.size()-2]))
                h.pop_back();
            h.push_back(i);
        }
        set<pii> s;for(auto i : h)s.insert(i);
        n = s.size();
        vector<vector<int>> ans;ans.resize(n);
        for(int i=0;i<n;i++)ans[i].resize(2);
        int id = 0;
        for(auto i : s){ans[id][0] = i.ff;ans[id][1] = i.ss;id++;}
        return ans;
    }
};
```
