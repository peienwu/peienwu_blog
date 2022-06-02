---
title: 資芽第六週：分治算法
date: 2021-4-10
tags: 
    - 資芽筆記
    - 分治
categories: 資訊之芽筆記
mathjax: true
---

## 上課內容

分治法，一開始講了三個證明遞迴複雜度的方法：

1. **取代法：**
 三步驟：Guess, Verify, Solve
 靠經驗假設，並驗證（加一些常數之類的）
2. **遞迴樹法**
 不太嚴謹，提供猜答案想法，最後用取代法驗證
 畫出遞迴樹
3. **主定理（Master Theorem）**
 比較f(n) 和 n^(logba)的關係
 大於等於小於有三個複雜度
<!--more-->

還有例題：

1. **區間連續最大和**
 <https://zerojudge.tw/ShowProblem?problemid=d784>
 中間切一半，max(左右最大值,跨過中間的最大值)
 時間複雜度：O(NlogN)
 有辦法O(n)?
2. **多項式乘法**
 Tioj 1064
 FFT? （快速點的乘法）
 Karatsuba algorithm，複雜度O(N^1.58)，還可以用在矩陣乘法
3. **平面最近點對**
    cf429D，Tioj
 平面分治演算法很多都這樣做，先照x座標排序，中間切割：
 max(兩點都在左邊、兩點都在右邊、跨過分割線答案)
 求跨過分割線答案：照y座標排序，距離超過d的不要看，因為附近的點是有限個（最近點距離d），大概最多只需要看7個點，可以在常數時間看完所有點
5. **尋找第k大**
    O(N)尋找第k大-利用分治

## 上機作業

### 逆序數對

[題目連結](https://neoj.sprout.tw/problem/125/)
這是要求逆序數對的兩個數字的和，總共卡了兩個地方：

1. TLE 計算和的複雜度爆了
2. WA 數字溢位後爆了

第一點的解決方式：對於每一層利用O(N)的時間算前墜和
第二點的解決方式：每做完一個運算就mod 一次

```cpp=
#include <bits/stdc++.h>
#define int long long
#define N 1000005
using namespace std;
int ans = 0,n,arr[N],pre[N];

void merge_sort(int l,int r){
    if(r-l<=1)return;
    int mid = (l+r)/2;
    merge_sort(l,mid);
    merge_sort(mid,r);
    
    int nl = l,nr = mid,ind = 0,temp[r-l];
    pre[l-1] = 0;
    for(int i=l;i<=mid;++i)pre[i] = pre[i-1]+arr[i];
    for(;nl<mid;nl++,ind++){
        while(arr[nr]<arr[nl] && nr<r){
            int times = mid-nl;//逆序數對的數量
            ans+=pre[mid-1]-pre[nl-1];
            ans+=arr[nr]*times;
            ans = ans%10000019;
            temp[ind] = arr[nr];
            nr++;ind++;
        }
        temp[ind] = arr[nl];
    }
    while(nr<r){//把剩下沒放的放進去
        temp[ind] = arr[nr];
        nr++;ind++;
    }
    for(int i=0;i<r-l;++i)arr[i+l] = temp[i];
}
signed main(){
    cin>>n;
    memset(arr, 0, sizeof(arr));
    for(int i=1;i<=n;++i)scanf("%lld",&arr[i]);
    merge_sort(1,n+1);
    printf("%lld\n",ans%10000019);
}
```

### 好的序列

[題目連結](https://neoj.sprout.tw/problem/789/)
題目簡單來說，就是給定n，要構造出一個1~n的序列，其中不包含3個數字以上的等差數列
**解題想法：** 我看不懂資芽的解法QQ
上網扣別人的扣，看幾乎都是同樣一個寫法，把奇數項的放前面，偶數項的放後面，之後不斷分治下去
直接實作然後AC ，於是我一直想到底要怎麼證明這個方法是正確的
於是我大概想到了一個上黑色白色的一個算合理的推倒吧
{% note success %}
首先，我們把長度為$n$的序列分別交錯塗上黑白
![](https://i.imgur.com/potNo28.png)
再來，把長度為$n$的序列畫一條線分成$[1,\dfrac{n}{2}]$ 與$[\dfrac{n}{2}+1,n]$兩個序列，把奇數次項的數字放入第一個序列，再把偶數次項的序列放入第二個
![](https://i.imgur.com/3oRNe5R.png)
我們可以發現，考慮橫跨中間線的兩邊是否有可能形成等差數列，可以發現是不可能，因為如果要橫跨兩邊，勢必要從一邊選出兩個，另一邊選一個，而同顏色的差為偶數，不同顏色的差必為奇數，因此**不可能構造出橫跨兩邊的等差數列**
但可以明顯發現，同一顏色是等差數列，其實只要分兩邊分別遞迴下去就可以了，遞迴處理完就是解答
![](https://i.imgur.com/EJTJh8I.png)
遞迴直到長度為2時就可以return 了
{% endnote %}

程式碼：

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define int long long
#define N 100005
using namespace std;
int ans = 0,n,arr[N],temp[N];

void DC(int l,int r){
    if(r-l<=1)return;
    int ind = l,mid = (l+r)/2;
    for(int i=l;i<=r;i++)temp[i] = arr[i];
    for(int i=l;i<=r;i+=2)arr[ind++] = temp[i];
    for(int i=l+1;i<=r;i+=2)arr[ind++] = temp[i];
    DC(l, mid);
    DC(mid+1, r);
}
signed main(){
    ios;
    cin>>n;
    for(int i=1;i<=n;i++)arr[i] = i;
    DC(1, n);
    for(int i=1;i<=n;i++)cout<<arr[i]<<" ";
    cout<<endl;
}
```

### 王老先生有塊地喔

[題目連結](https://neoj.sprout.tw/problem/124/)
這一題最需要關注的就是把問題縮小的關鍵
大範圍不會做，把規模縮到$2\times2$ 大小的方格總可以做了吧」
想辦法讓每一個$2\times2$ 大小的方格都有一格是被填滿的
剩下三格就很容易放進去
**注意：** 行跟列的維護很重要，一開始就要想好要怎麼樣定義儲存格，一維二維該放什麼

```cpp=
#include <bits/stdc++.h>
bool maze[1025][1025];

void DC(int,int,int);
void solve(int,int,int);

void solve(int N,int X,int Y){
    memset(maze, 0, sizeof(maze));
    maze[X][Y] = 1;
    DC(1,1,N);
}
void DC(int x,int y,int n){
    if(n<=1)return;
    int new_N = n/2,x_pos=0,y_pos=0;
    for(int i=x;i<x+n;i++){
        for(int j=y;j<y+n;j++){
            if(maze[i][j] == 1){
                x_pos = i;
                y_pos = j;
                break;
            }
        }
    }
    int pos;
    if(x_pos>=x+new_N){
        if(y_pos>=y+new_N)pos = 4;
        else pos = 3;
    }
    else{
        if(y_pos>=y+new_N)pos = 2;
        else pos = 1;
    }
    int nx = x+new_N,ny = y+new_N;
    switch (pos) {
        case 1:
            Report(nx, ny, nx-1, ny, nx, ny-1);
            maze[nx][ny] = 1;
            maze[nx-1][ny] = 1;
            maze[nx][ny-1] = 1;
            break;
        case 2:
            ny = ny-1;
            Report(nx, ny, nx-1, ny, nx, ny+1);
            maze[nx][ny] = 1;
            maze[nx-1][ny] = 1;
            maze[nx][ny+1] = 1;
            break;
        case 3:
            nx = nx-1;
            Report(nx, ny, nx+1, ny, nx, ny-1);
            maze[nx][ny] = 1;
            maze[nx+1][ny] = 1;
            maze[nx][ny-1] = 1;
            break;
        case 4:
            ny = ny-1;nx = nx-1;
            Report(nx, ny, nx+1, ny, nx, ny+1);
            maze[nx][ny] = 1;
            maze[nx+1][ny] = 1;
            maze[nx][ny+1] = 1;
            break;
        default:
            break;
    }
    DC(x, y, new_N);
    DC(x+new_N, y, new_N);
    DC(x, y+new_N, new_N);
    DC(x+new_N, y+new_N, new_N);
}
```

### 糟糕陣列

[題目連結](https://neoj.sprout.tw/problem/128/)
其中一種構造方式：對於第i列第i行的「糟糕陣列」，必須滿足聯集為 ${1,2,...,2k-1}$的數字，我們可以先構造出2*2的糟糕陣列，把它複製到右邊（加上邊長）、右下、與下方（加上邊長，複製完之後因為會有重複一個數字，扣掉就好
![](https://i.imgur.com/4G3GaOA.png)

```cpp=
#include <bits/stdc++.h>
using namespace std;
int maze[1026][1026];

void solve(int);
void DC(int n);

void solve(int N){
    memset(maze, 0, sizeof(maze));
    maze[1][1] = 1;
    maze[1][2] = 2;
    maze[2][1] = 3;
    maze[2][2] = 1;
    int temp = 4;
    while(temp<=N){
        DC(temp);
        temp = temp*2;
    }
    for(int i=1;i<=N;i++){
        for(int j=1;j<=N;j++){
            Report(maze[i][j]);
        }
    }
}

void DC(int n){
    int now_N = n/2;
    for(int i=1;i<=now_N;i++){
        for(int j=1;j<=now_N;j++){
            maze[i+now_N][j] = maze[i][j]+n;
            maze[i][j+now_N] = maze[i][j]+n;
            maze[i+now_N][j+now_N] = maze[i][j];
            if(maze[i][j]==1)maze[i][j+now_N]--;
        }
    }
}
```

## 手寫作業

**增倍算法！**
主要是介紹動態陣列，分析複雜度等等的東西
上一週的手寫作業被扣爆！
