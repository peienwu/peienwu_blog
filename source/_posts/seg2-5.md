---
title: "[題解]TIOJ 1224 矩形覆蓋面積計算"
date: 2021-1-8
tags: 
    - 線段樹
    - 題解
categories: 線段樹題解
mathjax: true
---

### 1224 . 矩形覆蓋面積計算
<!-- more -->
[題目連結](https://tioj.ck.tp.edu.tw/problems/1224)
[Submission2:AC](https://tioj.ck.tp.edu.tw/submissions/261541)

> 題意：給你平面上n個矩形，請求出它們覆蓋的總表面積。

這一題所使用的技巧是<font color="#f00">掃描線</font>以及<font color="#f00">線段樹</font>，下圖中的水平藍色線即為掃描線，由y=0開始往上掃描，當遇到了矩形的邊，利用線段樹查詢區間內當前的矩形寬度，乘上兩掃描線的高度差即為面積。當然，掃描線也可以使用垂直方向的線段由左而右的掃描，實作細節是一樣的。

![](https://i.imgur.com/pAsHmXd.jpg)

#### 線段樹維護

##### 方法一

我們可以定義線段樹$seg[cur]$為區間$[l,r]$中有被**矩形覆蓋的大小**有多大，也就是圖中當前掃描線對應到的區域的寬度。這樣子維護有一個問題，當我們直接用$seg[cur]$儲存答案，我們在修改的時候沒有辦法確切知道這段區間被覆蓋的情況。

下圖為一種模擬的情況，每一個區間的數字代表著非0的數字個數，也就是它的寬度。今天我們要對區間$[4,6]$加減值，將區間拆成$[4,4]$跟$[5,6]$，這時候區間$[3,4]$的數值是1，我們卻不知道到底是3還是4是有被覆蓋到的，必須要遞迴下去到葉節點才能得到完整的覆蓋情況，這時候每一次加減值的複雜就會提升到$O(n)$，因此不能以這種方式維護。

![](https://i.imgur.com/DOeuFyx.png)

##### 方法二

有別於第一種方法對$seg[id]$進行維護，我們可以多開一個區間 $tag$ 來紀錄被矩形覆蓋的情況。下圖有3個矩形，其中的數字代表每一塊區域被覆蓋的情況，這邊使用了$tag$來紀錄（他是附在區間上的，不會像圖中一樣的方式呈現）。tag的數值為非負整數，紀錄當前區間有多少矩形覆蓋在上面，用$tag$來輔助維護$seg[id]$可以在$O(logn)$的時間進行修改與查詢。

![](https://i.imgur.com/xGuEmB4.jpg)

以下程式碼是是 $tag$ 的轉移，當大的區間的tag值不為0，代表有一個矩形曾完整覆蓋這個區間，這時候可以直接回傳區間大小，否則即回傳左右節點的$sed[left],seg[right]$的數值。

這邊定義$seg[id]$為：「考慮 id 的子孫們（不含 id 本身）的所有 tag 值，假設這些子孫只有被tag值作用過，共有多少非0的數字」。

```cpp=
seg[cur].val = (seg[2*cur].tag?mid-l:seg[2*cur].val)
              +(seg[2*cur+1].tag?r-mid:seg[2*cur+1].val);
```

#### 實作方法

##### 矩形維護

首先是維護矩形的方法。我們一個矩形總共要維護四個東西：矩形左界x1、矩形右界x2、矩形上下界的y座標（分上下兩條），這兩條邊是下界或是上界val。為什麼要水平方向要分兩條討論？是因為下界代表進入，當掃描線掃到這一條邊的時候表示我們要新增區間 $[x1,x2)$ 進入線段樹；反之如果掃到了上界，則表示離開這個矩形，在線段樹中扣掉區間 $[x1,x2)$。

```cpp=
struct Node{    //每一個矩陣分成上下兩條邊
    int x1;     //矩形左界x1
    int x2;     //矩形右界x2
    int y;      //矩形y座標（分上下兩邊）
    int val;    //val = ±1(進入代表1、離開代表-1)
    
}arr[2*N];
```

上下界我們利用val維護，當 $val=1$ 時表示是矩形的下界； $val=-1$ 則是矩形上界，這兩個搭配在一起剛好就可以用線段樹區間加值的方式進行操作！總共有 $n$ 個矩形，因此我們要掃描線總共掃描 $2n$ 條線段。

##### 線段樹

一樣對值域（這題是1000000）的4倍開了線段樹，同時維護一個非負整數 $tag$ 表示區間被覆蓋的情況。當每一次修改完成之後，我們可以直接取用根節點 $seg[1]$ 的數值表示寬度（非0的個數）！

```cpp=+
//seg[i]表示i的左右兩子樹的區間非0的個數
struct node{    //建立線段樹
    int val;    //維護非0個數
    int tag;    //使用tag紀錄區間被覆蓋次數
}seg[4*M];
```

接下來就是在程式執行的過程中將 $2n$ 條邊依照y座標進行排序 $O(nlogn)$，接著依序使用掃描線搭配線段樹的修改，計算矩形的面積。最後就是輸出加起來的答案。

{% note info %}
**Debug 小錯誤**
[Submission1:WA](https://tioj.ck.tp.edu.tw/submissions/261512)
可以看到有一筆測資過不了，95分QQQ

![](https://i.imgur.com/1UNrejV.png)

後來debug之後發現到，因為我是對每一個矩形先輸入下界之後才是上界，當我在排序的過程中，上界有可能有機會跑到下界之前，造成 $tag$ 被扣到負的情況，但在定義中可以清楚知道 $tag$ 是非負整數造成錯誤。因此只要把排序的過程改成 stable_sort() 即可！

```cpp=
stable_sort(arr,arr+(n<<1),cmp);
```

{% endnote %}

最後終於是程式碼的部分，以下：

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0);
#define N 100005
#define M 1000001
#define lld long long
using namespace std;
int n;

struct Node{    //每一個矩陣分成上下兩條邊
    int x1;     //矩形左界x1
    int x2;     //矩形右界x2
    int y;      //矩形y座標（分上下兩邊）
    int val;    //val = ±1(進入代表1、離開代表-1)
    
}arr[2*N];

//seg[i]表示i的左右兩子樹的區間非0的個數
struct node{    //建立線段樹
    int val;    //維護非0個數
    int tag;    //使用tag紀錄區間被覆蓋次數
    
}seg[4*M];

bool cmp(Node a, Node b){
    return a.y<b.y;
}

//對區間[ql,qr)進行加值val
void modify(int cur,int l,int r,int ql,int qr,int val){
    if(r <= l || ql >= r || qr <= l)return;
    if(ql <= l && qr >= r){
        seg[cur].tag += val;
        return;
    }
    int mid = (l+r)/2;
    modify(2*cur,l,mid,ql,qr,val);
    modify(2*cur+1,mid,r,ql,qr,val);
    
    //左右節點如有tag表示被完全覆蓋，直接加上區間大小，否則加上seg[左右子樹]
    seg[cur].val = (seg[2*cur].tag?mid-l:seg[2*cur].val)
                  +(seg[2*cur+1].tag?r-mid:seg[2*cur+1].val);
}

int main(){
    ios;
    memset(arr,0,sizeof(arr));
    memset(seg,0,sizeof(seg));
    
    cin>>n;                                   //依序輸入左右下上：x1,x2,y1,y2
    for(int i=0;i<(n<<1);i+=2){
        int x1,x2,y1,y2;cin>>x1>>x2>>y1>>y2;
        arr[i] = (Node){x1,x2,y1,1};          //插入矩形下邊，帶入val = 1
        arr[i+1] = (Node){x1,x2,y2,-1};       //上邊要val = -1
    }
    stable_sort(arr,arr+(n<<1),cmp);          //依照y座標由小到大排序
    
    int y0 = 0,val = 0;                       //有下而上的枚舉所有水平邊
    lld ans = 0LL;                            //上一條y的座標，計算高，val為矩形結合起來的寬
    for(int i=0;i<(n<<1);i++){                //枚舉2n條y的邊
        ans += (lld)(arr[i].y-y0)*val;        //計算面積（寬*高）
        modify(1,0,M,arr[i].x1,arr[i].x2,arr[i].val);
        y0 = arr[i].y;
        val = seg[1].val;                     //修改後（下一輪）的矩陣寬度
    }
    cout<<ans<<'\n';
}
```
