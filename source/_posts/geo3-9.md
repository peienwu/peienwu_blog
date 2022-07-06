---
title: "[題解]Leetcode 478 Generate Random Point in a Circle"
date: 2021-1-5
tags: 
    - Leetcode
    - 題解
categories: Leetcode題解
mathjax: true
---

### 478 Generate Random Point in a Circle
<!--more-->
[題目連結](https://leetcode.com/problems/generate-random-point-in-a-circle)

直接Random半徑會出事（可能不夠亂，或是半徑太小），如果random面積之後算半徑才OK。

```cpp=
class Solution {
public:
    double R,X,Y;
    Solution(double radius, double x_center, double y_center) {
        R = radius;X = x_center;Y = y_center;
        srand(time(NULL));
    }
    
    vector<double> randPoint() {
        double Area = rand() * R * R * M_PI / (RAND_MAX + 1.0);
        double r = sqrt(Area / M_PI);
        double theta = 2.0 * M_PI * rand() / (RAND_MAX + 1.0);
        vector<double> ans;
        ans.push_back(X + r * cos(theta));
        ans.push_back(Y + r * sin(theta));
        return ans;
    }
};

/**
 * Your Solution object will be instantiated and called as such:
 * Solution* obj = new Solution(radius, x_center, y_center);
 * vector<double> param_1 = obj->randPoint();
 */
```
