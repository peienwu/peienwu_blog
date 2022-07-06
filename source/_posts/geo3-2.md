---
title: "[題解]Leetcode 223 Rectangle Area"
date: 2021-1-5
tags: 
    - Leetcode
    - 題解
categories: Leetcode題解
mathjax: true
---

### 223 Rectangle Area
<!--more-->
[題目連結](https://leetcode.com/problems/rectangle-area)

```cpp=
class Solution {
public:
    int computeArea(int ax1, int ay1, int ax2, int ay2, int bx1, int by1, int bx2, int by2) {
        int x = max(min(ax2,bx2) - max(ax1,bx1),0);
        int y = max(min(ay2,by2) - max(ay1,by1),0);
        int ans = (ax2 - ax1)*(ay2 - ay1)+(bx2 - bx1)*(by2 - by1)-x * y;
        return ans;
    }
};
```
