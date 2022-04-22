---
title: 以C++實作數獨問題（Sudoku）
date: 2021-3-7
tags: 
    - 數學
    - 數獨
    - DFS
categories:
	- C++基礎主題
	- 枚舉

mathjax: true
---

資訊之芽的上課內容，用了幾節下課加回家的時間把程式實作出來
用紙跟筆解數獨的過程就是把所有可能剔除明顯不可能的之後下去試，如果遇到問題就換下一種方式
![](https://i.imgur.com/WrGCOnx.png)
<!--more-->
用程式模擬也是一樣的概念，先把明顯不可能的情況剔除，接下來就一一透過DFS搜尋下去，如果碰壁就回朔回來
[NEOJ](https://neoj.sprout.tw/problem/62/)、[TIOJ](https://tioj.ck.tp.edu.tw/problems/1025)（他的範測給得不是很好閱讀XD

## 架構

先想出以下的實作步驟，就可以透過程式碼實現

1. 讀測資並處理
2. 從左到右、由上而下，找出下一個要填入數字的位置
3. 對每一個點要知道有哪些數字可以被放入（把明顯不行的剔除）
4. 從左上角的空格開始DFS

大概就是這一些實作步驟，如果用物件導向class 把所有東西包起來，~~看起來比較厲害~~
**DFS 剪枝！**

**整個大致架構：**

```cpp=
class Sudoku{
private:
    int maze[9][9];
    bool flag = false;
    
public:
    void print(string s);
    void scan_maze();
    vector<int> select(int, int);
    int next_empty(int, int);
    void solving();
    void dfs(int row, int col );
};
```

這可以讓main函式顯得更簡單：

```cpp=
int main(){
    Sudoku sodoku1;//宣告物件
    sodoku1.scan_maze();//讀入測資
    sodoku1.print("begin");//印出目前數獨（未解）
    sodoku1.solving();//開始解
}
```

## 輸入與輸出

設計一個印出數獨的函式，讓輸出方便閱讀一點（一行81個字元有夠醜。。。

```cpp=
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
```

接下來是讀入測資，0代表空格

```cpp=
void Sudoku::scan_maze(){
    for(int i=0;i<9;i++){
        for(int j=0;j<9;j++){
            char temp;cin>>temp;
            if(temp=='.')maze[i][j] = 0;
            else maze[i][j] = temp -'0';
        }
    }
}
```

有了**好的輸出**可以讓眼睛比較輕鬆一點Q

## 檢查哪些數字可以試試

這一個函式主要是實現上面的第三點，把明顯不行（在同一行、列、方格）出現過的數字剔除

```cpp=
vector<int> Sudoku::select(int r, int c){
    bool box[9]={0};//把出現過的數字紀錄下來
    
    for(int i=0;i<9;i++){
        if(maze[i][c]>0)box[maze[i][c]-1] = 1;//同一列
        if(maze[r][i]>0)box[maze[r][i]-1] = 1;//同一行
    }
    int row_start = 3*(r/3),col_start = 3*(c/3);//方格起始位置

    for(int i=0;i<3;i++){//同一方格
        for(int j=0;j<3;j++){
            if(maze[row_start+i][col_start+j]>0)
                box[maze[row_start+i][col_start+j]-1] = 1;
        }
    }
    vector<int> ans;
    for(int i=0;i<9;i++)
        if(!box[i])ans.push_back(i+1);
    return ans;//把可以試試看得數字push進vector後回傳
}
```

在遞迴的時候很需要知道下一個要填空的位置在哪裏，又題目要求如果有多組解要輸出**字典序最小**的解，所以要從左而右再由上而下開始找

```cpp=
int Sudoku::next_empty(int row, int col){
    int ind = col;
    for(int i=row;i<9;i++){
        while(ind<9){
            if(maze[i][ind]==0){//找到0就停止
                int pos = i*9+ind;
                return pos;
            }
            ind++;
        }
        ind = 0;
    }
    return -1;//找不檔任何空格，已經填滿
}
```

## 開始遞迴

DFS!
跟八皇后問題很像，都只不過八皇后問題只有**放跟不放**的問題，而數獨是要決定**放什麼**進去
首先，找到第一個空格之後DFS，在DFS結束後記得要確認數獨<font color="#f00">結果有沒有符合數獨原本的定義</font>(要不然只會對第一個子題QQ)
{% note success %}
Q: 明明空格是一個一個填上去，為啥還要再檢查一次？
A: 經過一小時的debug 之後發現，他給的測資不一定合法呀！
可能測資只有第一行第一格一空格，很高興填上去然後說 YA!解完了，但其實在第九行第九格出現了重複數字?!根本不會檢查到啊，但對第一行第一格一空格來說，一切都符合規則、很美好
{% endnote %}

```cpp=
void Sudoku::solving(){
    int first_empty = next_empty(0, 0),nr,nc;
    nr = first_empty/9; nc = first_empty%9;//找到出發點
    dfs(nr, nc);//dfs下去
    bool f = false;
    for(int i=0;i<9;i++){//81個數字再check一次
        for(int j=0;j<9;j++){
            vector<int> v = select(i,j);
            if(v.size()>0)f = true;//DFS完正常大小要是0
        }
    }
    if(!flag || f)cout<<"No solution."<<endl;//無解
    else print("solved");//印出解
    flag = false;
}
```

對每一種可能進行遞迴：

```cpp=
void Sudoku::dfs(int row, int col ){
    int pos = next_empty(row, col),nr,nc;
    if(pos == -1){
        flag = true;//有一組解了
        return;
    }
    vector<int> candidate = select(row, col);
    int len = candidate.size();
    
    for(int i=0;i<len;i++){
        maze[row][col] = candidate[i];
        pos = next_empty(row, col);
        nr = pos/9; nc = pos%9;
        dfs(nr, nc);
        if(flag)return;//如果要輸出很多組解的話可以刪掉這行
    }
    maze[row][col] = 0;
}
```

---
這份程式碼只是為了上傳oj，所以只需要一種情況就好
[完整程式碼在這](https://pastebin.com/bxhCmAPV)
不過，也可以用程式把每一種方法都列出來，數量可以多到不可想像！(我用程式跑了一下，結果直接它直接不理我，上網查才發現 $5\times 10^{27}$之多，天荒地老也算不完！）

這是測試用的數獨：
![](https://i.imgur.com/fYCm4Vq.jpg)

這是用線上解數獨的軟題跑出來的結果：
![](https://i.imgur.com/twbkUm1.png)
這是程式實測跑出來的結果，都吻合：
![](https://i.imgur.com/7I2kwtZ.png)

## 相關延伸：八皇后問題

[題目連結](https://leetcode.com/problems/n-queens-ii/)
這一份程式碼是leetcode八皇后問題的解。像下面這一張圖就是一組合法的解，透過遞迴可以找出每一組解。

![](https://i.imgur.com/gEH8Zyo.png)

網路上的資料指出這是一個$NP-Complete$的問題，表示並沒有多項式時間的解法。透過2個簡單的函數（是否可行、遞迴函數）即可實作出來。以下表格給出n為1到11的合法解個數：

| 1    | 2    | 3    |  4  |  5  |  6  |  7  |  8  |  9  |  10  |  11  |
| ---- | ---- | ---- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 0 | 0 |  2  |  10   |  4  |  40   |  92  | 352 | 724 | 2680 |

可以利用這個解直接丟原本的題目，就可以贏過78.93%的Submission（還是乖乖用遞迴寫啦） XD
![](https://i.imgur.com/SMItJxP.png)

以下是用遞迴寫的程式碼：

```cpp=
class Solution {
public:
    vector<vector<int>> temp;
    int ans = 0;
    void turn_zero(int n){
        vector<int> vec(n,0);
        for(int i=0;i<n;i++){
            temp.push_back(vec);
        }
    }
    
    bool isSafe(int x,int y,int n){
        for(int i=0;i<n;i++){
            for(int k=0;k<n;k++){
                if(x==i && y==k)continue;
                if(temp[i][k])
                    if(abs(i-x)==abs(k-y)||y==k)
                        return false;
            }
        }
        return true;
    }
    
    void Queen_Problem(int n){
        for(int i=0;i<n;i++){
            temp[0][i] = 1;
            DFS(1,n);
            temp[0][i] = 0;
         }
    }

    void DFS(int row,int n){
        if(row==n){
            ans++;
            return;
        }
        for(int i=0;i<n;i++){
            if(isSafe(row,i,n)){
                temp[row][i] = 1;
                DFS(row+1,n);
                temp[row][i] = 0;
            }
        }
    }

    int totalNQueens(int n) {
        turn_zero(n);
        Queen_Problem(n);
        return ans;
    }
};
```
