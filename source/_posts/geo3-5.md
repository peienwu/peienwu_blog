---
title: "[題解]Leetcode 593 Valid Square"
date: 2021-1-5
tags: 
    - Leetcode
    - 題解
categories: Leetcode題解
mathjax: true
---

### 593 Valid Square
<!--more-->
[題目連結](https://leetcode.com/problems/valid-square)

```cpp=
class Solution {
public:
    
    int dis(vector<int>& p1, vector<int>& p2){
        int x = p1[0] - p2[0],y = p1[1] - p2[1];
        return x * x + y * y;
    }
    
    bool validSquare(vector<int>& p1, vector<int>& p2, vector<int>& p3, vector<int>& p4) {
        map<int,int>mp;     //長度、個數
        mp[dis(p1,p2)]++;
        mp[dis(p1,p3)]++;
        mp[dis(p1,p4)]++;
        mp[dis(p2,p3)]++;
        mp[dis(p2,p4)]++;
        mp[dis(p3,p4)]++;
        return mp.size() == 2 && mp.begin()->second == 4;
    }
};
```
