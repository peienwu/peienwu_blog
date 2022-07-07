---
title: "[題解]ZJ d537 染色遊戲"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 染色遊戲—台北縣98資訊學科能力競賽
<!--more-->
[題目連結1](https://neoj.sprout.tw/problem/46/)[題目連結2](https://neoj.sprout.tw/problem/46/)
> 一題好題勝過百題水題

現在終於體會到這一句話的精髓！這是一題難題呀（neoj的測資很緊）
首先先看**顏色儲存的方式**
如果我想要讓混色時一個運算進行，那要如何以數字形式儲存顏色就顯德相當重要
可以發現，用加法並不是個粉好的方法，因為顏色的數字有可能超過7
因此可以用取OR的方式來完成：

>YELLOW : 001
BLUE : 010
GREEN : 011
RED : 100
ORANGE : 101
PURPLE : 110
DARK(BLACK) : 111

如果以這樣的編碼方式，剛剛好可以以OR運算來就可以模擬染色（這應該只能用觀察出來吧）
有了顏色的編碼方式之後，接下來就是處理**點擴散**的問題
由 $n<=1000$ 可知，如果要用2個for迴圈走訪整個畫布是不可行的
因為這樣就已經$10^6$ 多幾個常數就TLE了。
不能走訪每一個點，就只能好好的把要看的點push進queue裡面
每一次針對要找的節點，就能節省不必要的運算

最後，要怎麼維護**點**呢？
用一個*struct* 儲存，除了座標之外，就是出現的時間還有顏色
{% note success %}
**我遇到TLE 或 WA的BUG：**

1. 用 $n^2$ 掃描整個畫布—TLE
2. push進一個struct時要在名字部分**括號起來**：

```cpp=
queue<node> q;
q.push((node){1,2,3,4});
```

3. 沒有加上遇到減少就break的條件，所以每一次都必須要整個畫布都變成黑色才會輸出—TLE
4. 沒有紀錄一個點有沒有被三原色走過，一個點可能被走過很多次—TLE
5. 邊界要好好維護，一個較方便的作法，讓index都從1開始，這樣相加相減變成-1 時才不會RE
6. 時間優化：明顯知道答案（如問D的情況），就直接輸出$n^2$就好
{% endnote %}

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0);
using namespace std;
int n,col[150],dx[8] = {-1,0,1,1,1,0,-1,-1},
    dy[8] = {-1,-1,-1,0,1,1,1,0},board[1005][1005];
bool visit[1005][1005][5];//三原色每一個點只能被走一次

int encode(char a){
    col['Y'] = 1;col['B'] = 2;col['G'] = 3;col['R'] = 4;
    col['O'] = 5;col['P'] = 6;col['D'] = 7;
    return col[a];
}
struct node{
    int x,y,time,color;
};

int main(){
    ios;
    int t;cin>>t;
    while(t--){
        cin>>n;
        queue<node> qq;
        memset(board, 0, sizeof(board));
        memset(visit, 0, sizeof(visit));
        char a;
        for(int i=0;i<3;i++){
            int b,c;cin>>a>>b>>c;b++;c++;
            qq.push((node){b,c,1,encode(a)});
            board[b][c] = encode(a);
        }
        cin>>a;
        int target = encode(a),cur_sum = 0,ans = 0,time = 1;
        if(a=='B'||a=='Y'||a=='R')cur_sum++;//當要求的是三原色，初始的數量可以加一
        
        if(a=='D'){
            cout<<n*n<<endl;
            continue;
        }//如果是D的話，只需要輸出n^2，可以節省不少時間
        while(!qq.empty()){//BFS 開始
            while(!qq.empty() && qq.front().time==time){//開始每一個時刻的模擬
                int nx = qq.front().x,ny = qq.front().y;
                for(int i=0;i<8;i++){
                    int x = nx+dx[i],y = ny+dy[i];
                    if(x<=0 ||x>n||y<=0||y>n||board[x][y]==7||
                       visit[x][y][qq.front().color])continue;//超出邊界或曾拜訪或已塗黑可跳過
                    qq.push((node){x,y,time+1,qq.front().color});//push下一時刻的node
                    int new_color = board[x][y]|qq.front().color;//比較新的顏色與舊的顏色
                    if(new_color!=board[x][y]){//顏色不同時要比較狀況
                        if(new_color==target)cur_sum++;//新顏色是但舊顏色不一樣 則加一
                        else if(board[x][y]==target)cur_sum--;//舊的是新的不是 則減一
                    }
                    board[x][y]|=qq.front().color;//對新方塊上色
                    visit[x][y][qq.front().color] = 1;//此顏色已經拜訪
                }
                qq.pop();//pop from queue
            }
            time++;//一輪模擬結束
            if(ans>cur_sum)break;//因為數量會先非嚴格遞增、後非嚴格遞減
            ans = max(ans, cur_sum);
        }
        cout<<ans<<endl;
    }
}

```
