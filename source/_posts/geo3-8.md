---
title: "[題解]Leetcode 858 Mirror Reflection"
date: 2021-1-5
tags: 
    - Leetcode
    - 題解
categories: Leetcode題解
mathjax: true
---

### 858 Mirror Reflection
<!--more-->
[題目連結](https://leetcode.com/problems/mirror-reflection)

```cpp=
class Solution {
public:
    int gcd(int a,int b){
        if(a == 0)return b;
        return gcd(b % a,a);
    }
    
    int mirrorReflection(int p, int q) {
        int len = p * q / gcd(q,p);
        int a = (len / p) % 2,b = (len / q) % 2;
        if(a == 0 && b == 1)return 0;
        else if(a == 1 && b == 1)return 1;
        else return 2;
        
    }
};
```
