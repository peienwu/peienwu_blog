---
title: "[題解]Leetcode 812	Largest Triangle Area"
date: 2021-1-5
tags: 
    - Leetcode
    - 題解
categories: Leetcode題解
mathjax: true
---

### 812 Largest Triangle Area
<!--more-->
[題目連結](https://leetcode.com/problems/largest-triangle-area)

```cpp=
class Solution {
public:
    
    int cross(int x1,int y1,int x2,int y2){
        return x1 * y2 - x2 * y1;
    }
    
    double area(int a,int b,int c,int d,int e,int f){
        double sum = 0;
        sum += cross(a,b,c,d);
        sum += cross(c,d,e,f);
        sum += cross(e,f,a,b);
        return abs(sum / 2.0);
    }
    
    double largestTriangleArea(vector<vector<int>>& points) {
        int n = points.size();
        double ans = 0;
        for(int i = 0;i < n;i++){
            for(int j = i + 1;j < n;j++){
                for(int p = j + 1;p < n;p++){
                    ans = max(ans,area(points[i][0],points[i][1]
                                      ,points[j][0],points[j][1]
                                      ,points[p][0],points[p][1]));
                }
            }
        }
        return ans;
    }
};
```
