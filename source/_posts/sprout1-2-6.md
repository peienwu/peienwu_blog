---
title: "[題解]NEOJ 48 二元搜尋樹"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 二元搜尋樹
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/48/)
很特別的一題，原來BST 用中序遍歷就是排序好的數列！
有了前序跟中序之後，就可以透過遞迴來還原整棵二元樹了！

```cpp=
#include <bits/stdc++.h>
using namespace std;
int n;

struct Node{
    Node *left;
    Node *right;
    int val;
};

Node* BinaryTree(int* preorder, int* inorder, int length){
    if(length==0)return NULL;
    Node *node = new Node;
    node->val = *preorder;
    
    int root_index = 0;
    for(;root_index<length;root_index++){
        if(inorder[root_index]==*preorder)break;
    }
    node->left = BinaryTree(preorder+1, inorder,root_index);
    node->right = BinaryTree(preorder+root_index+1, inorder+root_index+1,length-root_index-1);
    cout<<node->val<<endl;
    return node;
}

signed main(){
    int arr[2005],sorted[2005];
    while(cin>>arr[n]){
        sorted[n] = arr[n];
        n++;
    }
    sort(sorted, sorted+n);
    BinaryTree(arr, sorted, n);
}
```
