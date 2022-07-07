---
title: "[題解]TIOJ 1025 數獨問題"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 數獨
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/62/)
首先是數獨，很有趣，之前有做過類似的是**八皇后問題**
用到**DFS剪枝**，[筆記在這](https://peienwu.com/2021/03/07/Sudoku/)

```cpp=
#include <bits/stdc++.h>
using namespace std;

class Sudoku{
private:
    int maze[9][9];
    bool flag = false;
public:
    void print(string s);//
    void scan_maze();//
    vector<int> select(int, int);
    int next_empty(int, int);
    void solving();
    void dfs(int row, int col );
    
};
void Sudoku::print(string s){
    cout<<endl<<"====="<<s<<"======"<<endl;
    for (int i=0; i<9;i++){
        cout<<"|";
        for (int j=0;j<9;j++){
            cout<<maze[i][j];
            if(j%3==2)cout<<"|";
        }
        cout<<endl;
        if(i%3==2)cout<<"-------------"<<endl;
    }
}
vector<int> Sudoku::select(int r, int c){
    bool box[9]={0};
    for(int i=0;i<9;i++){
        if(maze[i][c]>0)box[maze[i][c]-1] = 1;
        if(maze[r][i]>0)box[maze[r][i]-1] = 1;
    }
    int row_start = 3*(r/3),col_start = 3*(c/3);
    for(int i=0;i<3;i++){
        for(int j=0;j<3;j++){
            if(maze[row_start+i][col_start+j]>0)
                box[maze[row_start+i][col_start+j]-1] = 1;
        }
    }
    vector<int> ans;
    for(int i=0;i<9;i++)
        if(!box[i])ans.push_back(i+1);
    return ans;
}
int Sudoku::next_empty(int row, int col){
    int ind = col;
    for(int i=row;i<9;i++){
        while(ind<9){
            if(maze[i][ind]==0){
                int pos = i*9+ind;
                return pos;
            }
            ind++;
        }
        ind = 0;
    }
    return -1;
}
void Sudoku::dfs(int row, int col ){
    int pos = next_empty(row, col),nr,nc;
    if(pos == -1){
        flag = true;
        return;
    }
    vector<int> candidate = select(row, col);
    int len = candidate.size();
    
    for(int i=0;i<len;i++){
        maze[row][col] = candidate[i];
        pos = next_empty(row, col);
        nr = pos/9; nc = pos%9;
        dfs(nr, nc);
        if(flag)return;
    }
    maze[row][col] = 0;
}
void Sudoku::scan_maze(){
    for(int i=0;i<9;i++){
        for(int j=0;j<9;j++){
            char temp;cin>>temp;
            if(temp=='.')maze[i][j] = 0;
            else maze[i][j] = temp -'0';
        }
    }
}
void Sudoku::solving(){
    int first_empty = next_empty(0, 0),nr,nc;
    nr = first_empty/9; nc = first_empty%9;
    dfs(nr, nc);
    bool f = false;
    for(int i=0;i<9;i++){
        for(int j=0;j<9;j++){
            vector<int> v = select(i,j);
            if(v.size()>0)f = true;
        }
    }
    if(!flag || f)cout<<"No solution."<<endl;
    else{
        print("solved");
    }
    flag = false;
}
int main(){
    Sudoku sodoku1;
    sodoku1.scan_maze();
    sodoku1.print("begin");
    sodoku1.solving();
}
```
