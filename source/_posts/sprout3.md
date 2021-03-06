---
title: 資芽第三週：基礎圖論、淹水問題
date: 2021-3-20
tags: 
    - 資芽筆記
    - 圖論
    - 淹水問題
categories: 資訊之芽筆記
mathjax: true
---

前兩次上課還沒來得及補齊，先來紀錄一下第三週的內容

## 上課內容

這一週的主題是基礎圖論、淹水問題(BFS)還有Heap!把這幾個單元排在一起真的是負擔很重QQ
<!--more-->
* 基礎圖論之前就有聽過，所以在寫題目感覺還好，不會很吃力
* Heap則是我第一次接觸到的資料結構，當周上課前把影片（介紹Binary Heap）之後，上課就直接講合併Heap、黑魔法Heap、左偏樹等等把我電爛的東西
* 淹水問題，上課沒有特別介紹，因為講師說他不喜歡*Flood Fill* 演算法所以只講了可以用 *dx* 和 *dy* 表示上下左右的方位，其他的都沒講，只能自己看影片了呀～

<font color="#f00">這一週是段考週呀</font> 我得花一些時間讀段考，不然段考要爆炸了...</font>

## 上機作業

### Heap 練習

[題目連結](https://neoj.sprout.tw/problem/59/)
刻一個heap嘛，不想刻就call stl( *std::priorityqueue* 就不放上來了)
自己刻一個binary heap ，沒有想象中的簡單，有些細節會不小心漏掉

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define int long long
using namespace std;
int t,top=1,heap[1000005];//complete binary tree

void push(int n){
    heap[top] = n;
    int father_pos = top/2,pos = top;
    while(father_pos>0 && heap[father_pos]>heap[pos]){
        swap(heap[father_pos], heap[pos]);
        pos = father_pos;
        father_pos/=2;
    }
    top = top+1;
}

int pop(){
    int ans = heap[1];
    heap[1] = heap[--top];
    heap[top] = 0;
    int now = 1,left = now*2, right = now*2+1;
    
    int temp = min(heap[right],heap[left]);
    while(heap[left]!=0 && heap[right]!=0 && heap[now]>temp){
        if(heap[now]>heap[right] && heap[left]>heap[right]){
            swap(heap[right], heap[now]);
            now = right;
        }
        else if(heap[now]>heap[left]){
            swap(heap[left], heap[now]);
            now = left;
        }
        right = now*2+1;
        left = now*2;
        temp = min(heap[right],heap[left]);
    }
    if(heap[right]!=0 && heap[now]>heap[right])swap(heap[right], heap[now]);
    else if(heap[left]!=0 && heap[now]>heap[left])swap(heap[left], heap[now]);
    return ans;
}
bool empty(){
    if(heap[1]==0)return 1;
    else return 0;
}
signed main(){
    ios;
    cin>>t;
    memset(heap, 0, sizeof(heap));
    while(t--){
        int a;cin>>a;
        if(a==1){
            int b;cin>>b;
            push(b);
        }
        else if(a==2){
            if(empty())cout<<"empty!"<<endl;
            else cout<<pop()<<endl;
        }
    }
}
```

### 哪裡有卦，哪裡就有源(TOJ 1101)

[題目連結](https://neoj.sprout.tw/problem/1101/)
明顯的二分圖水題喔～
用 dfs 把經過的點標出黑白，過程中如果遇到問題就表示非二分圖了

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define int long long
using namespace std;
int n;

bool visit[100005],color[100005];
vector<int> edge[100005];

bool dfs(int id, bool col){
    visit[id] = 1;
    color[id] = col;
    int len = edge[id].size();
    for(int i=0;i<len;i++){
        int temp = edge[id][i];
        if(visit[temp]){
            if(color[temp]==col)return 0;
            else continue;
        }
        if(!dfs(temp, !col))return 0;
    }
    return 1;
}
signed main(){
    ios;
    while(cin>>n && n){
        int m;cin>>m;
        for(int i=0;i<n;i++)edge[i].clear();
        memset(visit, 0, sizeof(visit));
        memset(color, 0, sizeof(color));
        while(m--){
            int a,b;cin>>a>>b;
            edge[a].push_back(b);
            edge[b].push_back(a);
        }
        bool f = 1;
        for(int i=0;i<n;i++){
            if(!visit[i]){
                if(!dfs(i, 0)){
                    f = 0;
                    break;
                }
            }
        }
        if(f)cout<<"NORMAL."<<endl;
        else cout<<"RAINBOW."<<endl;
    }
}
```

### 喵喵抓老鼠

[題目連結](https://neoj.sprout.tw/problem/44/)
這一題是用BFS找最短路徑，因為沒有權重，所以可以直接用BFS看多久會最先走到老鼠
x y 座標原本用2個queue 分別存x跟y ，沒想到MLE了，後來改用struct，還是MLE!!
不過在27行的地方有一個Bug，害我找了好久...去問了<font color="#f00">電神</font>，才發現 visit 應該要在push 的時候就紀錄了，不要等到pop的時候，不然很多重複的會被push 進去（簡單來說，一個位置只能被push一次）

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define MAX 102
using namespace std;
int n,sx,sy;
int dx[4]={-1,0,1,0}, dy[4]={0,-1,0,1};//左下右上
char maze[MAX][MAX];
bool visit[MAX][MAX];

struct node{
    int x,y,path;
};
queue<node> qq;
int bfs(){
    int now_x = sx, now_y = sy;
    qq.push(node{sx,sy,0});
    while(!qq.empty()){
        now_x = qq.front().x;
        now_y = qq.front().y;
        if(maze[now_x][now_y]=='@')return qq.front().path;
        int front = qq.front().path+1;
        for(int i=0;i<4;i++){
            int nx = now_x+dx[i],ny = now_y+dy[i];
            if(!visit[nx][ny] && maze[nx][ny]!='#'){
                qq.push(node{nx,ny,front});
                visit[nx][ny] = 1;
            }
        }
        qq.pop();
    }
    return -1;
}
signed main(){
    ios;
    while(cin>>n && n!=0){
        memset(visit, 0, sizeof(visit));
        while(!qq.empty())qq.pop();
        for(int i=0;i<n;i++)cin>>maze[i];
        
        for(int i=0;i<n;i++)
            for(int j=0;j<strlen(maze[i]);j++)
                if(maze[i][j]=='K'){
                    sx = i;sy = j;
                    break;
                }
        int ans = bfs();
        if(ans>=0)cout<<ans<<"\n";
        else cout<<"= =\""<<"\n";
    }
    return 0;
}
```

### 庭院裡的水池

[題目連結](https://neoj.sprout.tw/problem/42/)
看圖形有幾個連通圖的判斷的水題，可以用dfs 或bfs 做，小心index不要戳到負的

**BFS作法**

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int t;
bool maze[1005][1005],visit[1005][1005];
int dx[4]={-1,0,1,0}, dy[4]={0,1,0,-1};
queue<int> qx,qy;

void bfs(int x,int y){
    int now_x=x,now_y=y;
    qx.push(x);
    qy.push(y);
    while(!qx.empty()){
        now_x = qx.front();now_y = qy.front();
        visit[now_x][now_y]=1;
        for(int i=0;i<4;i++){
            int nx = now_x+dx[i], ny = now_y+dy[i];
            if(!visit[nx][ny] && maze[nx][ny]==1){
                qx.push(nx);
                qy.push(ny);
                visit[nx][ny] = 1;
            }
        }
        qx.pop();
        qy.pop();
    }
}
int main(){
    ios;
    cin>>t;
    while(t--){
        int a,b;cin>>a>>b;
        memset(maze,0,sizeof(maze));
        memset(visit,0,sizeof(visit));
        for(int i=1;i<=a;i++){
            for(int j=1;j<=b;j++){
                char temp;cin>>temp;
                if(temp=='#')maze[i][j]=0;
                else maze[i][j]=1;
            }
        }
        int ans = 0;
        for(int i=1;i<=a;i++){
            for(int j=1;j<=b;j++){
                if(maze[i][j]==1 && visit[i][j]==0){
                    bfs(i,j);
                    ans++;
                }
            }
        }
        cout<<ans<<endl;
    }
}
```

**DFS作法**

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int t;
bool maze[1005][1005],visit[1005][1005];
int dx[4]={-1,0,1,0}, dy[4]={0,1,0,-1};
queue<int> qx,qy;

void dfs(int x, int y){
    visit[x][y] = 1;
    for(int i=0;i<4;i++){
        int nx = x+dx[i], ny = y+dy[i];
        if(!visit[nx][ny] && maze[nx][ny]==1){
            dfs(nx, ny);
        }
    }
}
int main(){
    ios;
    cin>>t;
    while(t--){
        int a,b;cin>>a>>b;
        memset(maze,0,sizeof(maze));
        memset(visit,0,sizeof(visit));
        for(int i=1;i<=a;i++){
            for(int j=1;j<=b;j++){
                char temp;cin>>temp;
                if(temp=='#')maze[i][j]=0;
                else maze[i][j]=1;
            }
        }
        int ans = 0;
        for(int i=1;i<=a;i++){
            for(int j=1;j<=b;j++){
                if(maze[i][j]==1 && visit[i][j]==0){
                    dfs(i, j);
                    ans++;
                }
            }
        }
        cout<<ans<<endl;
    }
}
```

比較上面兩種方法的複雜度
![](https://i.imgur.com/WfcJ5I8.png)
BFS明顯優於DFS 啊！（遞迴本身來說就很耗記憶體?!應該是這題特殊的原因，不然應該用DFS 較優）

### 染色遊戲—台北縣98資訊學科能力競賽

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

## 手寫作業

這一週的手寫作業是介紹c++ 的記憶體使用方式，什麼變數應該會被存在什麼位置之類的，heap 與stack 還有虛擬記憶體，以及為什麼有時候寫dfs遞迴下去會RE的問題（我是還沒有遇過啦）
![](https://i.imgur.com/mFgJxBF.png)
總之終於沒有數學證明題了！

---
<font color="#f00">今天段考果然炸裂了QQQQQQQ</font>
