---
title: 2020資訊之芽—線段樹(Segment Tree)
date: 2021-8-21
tags: 
    - 2020資訊之芽
    - 2021暑假筆記
categories: 暑期算法筆記
mathjax: true
---

今年是2021，資芽的二階主題跟2020上的有很多的差別，因此會利用暑假把2020的東西也補一補！
## 課程內容
### 儲存
線段樹很多人都用陣列來實作，空間$O(n)$，在處理樹的左右節點也十分的方便。如果以1-base來儲存陣列這棵線段樹，左右節點可以用以下的index來表示。線段樹有一個優點就是它可以用儲存完滿二元樹的方式來儲存，雖然它不一定是完滿但會是平衡的一棵樹。左節點：$2n$，右節點$2n+1$。
![](https://i.imgur.com/PxXXqsl.png)
<!-- more -->

### 空間大小
一般來說線段樹的陣列會開$seg[4\times n]$，由以下證明得到的結果：

{% note default %}
對於一個有$2^k$的葉節點數的一棵樹，總共的節點共有 $\sum_{i=0}^k 2^i = 2^{k+1}-1$ 個節點。若今天陣列共有n個元素，則代表葉節點共有$2^{\lceil \log_2 n\rceil}$ 個，總節點數量共有$2^{\lceil \log_2 n\rceil+1}-1$ 個。
假設$n = 2^k+p,0≤p≤2^k$，則帶入前面公式 $2^{\lceil \log_2 n\rceil}=2^{k+1}$，根據前面推得的總節點數可以知道為 $2^{k+2}-1$，其值恆小於$4n$，因此開$4n$的陣列一定可以容納整個線段樹。
{% endnote %}

由以上推倒可以知道一般都會開$seg[4\times n]$，但是如果n剛好是二的冪次，其實只會用到$2n$的空間，有一半會被浪費掉，不過沒差，因為多開總比少開來得好！
[懶標線段樹程式碼連結](https://gist.github.com/peienwu/368723520e3c39e4e0edc27a0823da69)，懶標這東西實在折磨人，學長寫的一個堪稱不用下推的懶標程式碼，結果他是錯的！！！害我Debug超久，可惡:cry:
![](https://i.imgur.com/mEY8cFD.png)
這是學長的query程式碼，可以看到他回傳的是$(r-l)\times lazy[cur]$，這是錯誤的，因為不一定左右兩子樹都會被全部加上懶標的值（可能只會有部分交集），但這份code卻直接加上去，會多算而WA，要注意！
![](https://i.imgur.com/nsd2KVP.png)
更改版，對區間取聯集應該就對了！

### 實作部分
這個版本是用struct實作，比起用陣列實作，這個版本更為方便閱讀，容易理解。為了方便索引值查詢左右子樹，使用1-base來儲存線段樹，同時利用一個struct儲存每一個節點。同時，每一個區間採用的是左閉右開 $[l,r)$，區間和的實作如下：
```cpp=
struct Node{            //線段樹每一個節點
    int val=0,tag=0,sz; //val原本的值、tag懶標、sz區間大小
    int rv(){           //回傳實際節點的值
        return val+tag*sz;
    }
}seg[4*N];
```

#### 建立(Build)
分別遞迴建立左右子樹，同時更新每一個節點的大小sz。因為是左閉右開，直接左右相減r-l即可完成更新。
```cpp=+
void build(int l,int r,int cur){    //建立線段樹
    seg[cur].sz = r-l;              //更新節點的大小
    if(r<=l)return;                 //空區間回傳
    if(r-l==1){                     //設定當前節點的值
        seg[cur].val = arr[l];
        return;
    }
    int m = (l+r)/2;                //分別遞迴建立左右子樹
    build(l,m,2*cur);
    build(m,r,2*cur+1);
    seg[cur].val = seg[2*cur].val+seg[2*cur+1].val;
}
```

#### 下推(push)
懶標真是驚人的想法，在區間修改的時候以$O(logn)$在要修改的區間先懶標標記起來，等到要query的時候把懶標推下去，這樣可以確保區間修改的複雜度是$O(logn)$，同時query的時候順便下推，一來比較好實作、二來複雜度也不會有什麼改變， 因為下推的複雜度是$O(1)$。
```cpp=+
//把id的懶標在query的時候往下推
void push(int id){
    seg[2*id].tag += seg[id].tag;   //左子樹懶標更新
    seg[2*id+1].tag += seg[id].tag; //右子樹懶標更新
    seg[id].val = seg[id].rv();     //更新實際值
    seg[id].tag = 0;                //往下推完之後設定為預設
}
```
#### 區間詢問(query)
這裡的重點是下推，如果把懶標下推，可以確保區間修改的時間是$O(logn)$
```cpp=+
//區間詢問
int query(int cur,int l,int r,int ql,int qr){
    if(r<=l || ql>=r || qr<=l)return 0;         //空集合直接回傳
    if(ql<=l && qr>=r)return seg[cur].rv();     //包含在要詢問的範圍中，回傳實際值
    push(cur);                                  //將懶標在遞迴下去的過程中下推
    int mid = (l+r)/2;                          //遞迴查詢左右子樹
    return query(cur*2,l,mid,ql,qr)+query(cur*2+1,mid,r,ql,qr);
}
```

#### 區點修改(modify)
可以這樣理解，在區間修改中，透過遞迴完左右子樹拉上來的過程中，更新樹上較上方的節點的值（用懶標更新）。此時下方節點都還沒有被更新，等到query的時候會往下push順便把值也一起更新。
```cpp=+
//區間修改
void modify(int cur,int l,int r,int ql,int qr,int val){
    if (r<=l||ql>=r||qr<=l)return;
    if (ql<=l && qr>=r) {
        seg[cur].tag += val;               //將被完整包含的區間的懶標加上修改值
        return;
    }
    int mid = (l+r)/2;
    modify(cur*2,l,mid,ql,qr,val);          //修改左右子樹
    modify(cur*2+1,mid,r,ql,qr,val);        //遞迴完拉上來的過程中把上面的數值更新成有懶標的結果
    seg[cur].val = seg[2*cur].rv()+seg[2*cur+1].rv();
}
```
#### 單點修改(modify)
這一題沒有要求單點修改，所以以下是RMQ的單點修改。概念較為簡單，用$O(logn)$找到要修改的點，直接修改即可。
```cpp=
void modify(int cur,int l,int r,int ind,int val){
    if(r<=l)return;
    if(r-l==1 && l==ind){
        seg[cur]=val;
        return;
    }
    int mid = (l+r)/2;
    if(ind<mid)modify(cur*2,l,mid,ind,val);
    else modify(cur*2+1,mid,r,ind,val);
    seg[cur] = min(seg[2*cur],seg[2*cur+1]);
}
```
### 動態開點

> 有一個長度是 $10^9$ 的序列，一開始裡面的元素都是$0$，有 $Q$ 筆操作，每一個操作是以下其中兩種：
> 1. 把之間的值都加上$v$ 
> 2. 詢問的區間和

開這麼多點顯然會MLE，既然使用陣列型儲存線段樹會爆，因為這麼大的值域顯然有許多點是不會被使用到的，那就改成使用指標型來開這個線段樹，以下為每一個節點的架構：

```cpp=
struct Node{
    Node *l;    //指向左子樹
    Node *r;    //指向右子樹
    int val;    //維護區間和
    
};//注意struct裡面可有struct的指標，但不能有實體
```


## 上機作業

### RMQ練習
[題目連結](https://neoj.sprout.tw/problem/80/)
RMQ = Range minimun query，也就是詢問一個區間的最小值。分析一下兩種不同作法的複雜度：
{% note default %}
對於數列 $a_n$ 共n項，k筆詢問，每一次詢問區間$[l,r]$的最大、最小值
**1. 暴力做**
時間複雜度：<font color="#f00">詢問$O(n)$</font>
對於每一筆詢問都暴力搜索，最多掃過n個數字，時間複雜度$O(kn)$，對於數字大一點的情況就會TLE
<br> 

**2. 分塊算法**

<br> 
**3. 線段樹**
時間複雜度：<font color="#f00">預處理 $O(n)$、詢問$O(logn)$</font>
首先是預處理建立線段樹，線段樹上約有$2n$個節點，因此空間複雜度是$O(n)$，也就表示預處理是$O(n)$，之後便可$O(logn)$查詢每一筆詢問。以下是各種操作複雜度：


* **初始建構**：所有節點恰會建構一次，每個節點 $O(1)$，配合節點樹可得為 $O(𝑛)$
* **單點修改**：該點的所有祖先節點都會被修改到，其他都不會被修改到，$𝑂(logn)$
* **區間查詢**：每筆詢問最多詢問到深度為 $O(logn)$ 的節點。在一次詢問中，每一層不會有超過2個節點被詢問，總複雜度為$𝑂(log𝑛)$
<br> 

**4. 稀疏表(Sparse Table)**
時間複雜度：<font color="#f00">預處理 $O(nlogn)$、詢問$O(1)$</font>
這個之後再看！
{% endnote %}

這一題就是基礎的要有支援區間查詢、單點修改的線段樹，也是最簡單的一種！
```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0);
#define N 1000000
#define INF 1e9
using namespace std;
int seg[4 * N],arr[N],n,m;

void build(int l,int r,int cur){
    if(r<=l)return;
    if(r-l==1){
        seg[cur] = arr[l];
        return;
    }
    int m = (l+r)/2;
    build(l,m,2*cur);
    build(m,r,2*cur+1);
    seg[cur] = min(seg[2*cur],seg[2*cur+1]);
}

//單點修改
void modify(int cur,int l,int r,int ind,int val){
    if(r<=l)return;
    if(r-l==1 && l==ind){
        seg[cur]=val;
        return;
    }
    int mid = (l+r)/2;
    if(ind<mid)modify(cur*2,l,mid,ind,val);
    else modify(cur*2+1,mid,r,ind,val);
    seg[cur] = min(seg[2*cur],seg[2*cur+1]);
}

//區間詢問
int query(int cur,int l,int r,int ql,int qr){
    if(r<=l || ql>=r || qr<=l)return INF;
    if(ql<=l && qr>=r){
        return seg[cur];
    }
    int mid = (l+r)/2;
    return min(query(cur*2,l,mid,ql,qr),query(cur*2+1,mid,r,ql,qr));
}

int main(){
    ios;
    cin>>m>>n;
    for(int i=1;i<=n;i++)cin>>arr[i];
    build(1,n+1,1);
    
    while(m--){
        int p;cin>>p;
        if(p==2){
            int x,y;cin>>x>>y;
            modify(1,1,n+1,x+1,y);
        }
        else if(p==1){
            int x,y;cin>>x>>y;
            int ans = query(1,1,n+1,x+1,y+2);
            cout<<ans<<endl;
        }
    }
}
```

### 最大連續和？
[題目連結](https://neoj.sprout.tw/problem/249/)
這題題結果我是WA在INF太大可能會溢位的問題，把INF調整成 $5\times 10^{14}$ 是差不多的

這一題要維護區間最大連續和，跟dp的最大連續和差了「區間」兩字，整個實作的方式跟複雜度就不一樣了。DP的作法如果直接套用在這一題的話，複雜度約為：$O(QN)$，絕對會炸裂，因此只能用線段樹維護這個東西。

{% note primary %}
**區間最大連續和**
在分治法的單元有提到分治法最重要的是假定左右兩側（對應過來就是兩子樹）是維護好的，也就是符合定義，要想的是要如何處理橫跨左右子樹的情況。
要如何知道橫跨中間的最大值呢？這時候我們可以維護4個數值，來更新每一個線段樹上的節點的數值。
1. **總和(sum)**
$cur.sum = left.sum+right.sum$
3. **最大前綴和(lmax)**
$cur.lmax = max(left.lmax,left.sum+right.lmax)$
5. **最大後綴和(rmax)**
$cur.rmax = max(right.rmax,right.sum+left.rmax)$
7. **區間最大連續和(tmax)**
$cur.tmax = max(left.tmax,right.tmax,left.rmax+right.lmax)$
{% endnote %}
維護最大後綴跟最大前綴和（首跟末有被取到的情況），就可以$O(1)$ 維護每一個節點的區間最大連續和，複雜度：詢問$O(logn)$，因此總時間$O(Qlogn)$就可以AC了!


```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0);
#define int long long
#define N 100005
#define INF 500000000000000
using namespace std;
int arr[N],n,m;

struct Node{
    int sum;    //區間總和
    int lmax;   //最大前綴和
    int rmax;   //最大後綴和
    int tmax;   //區間連續最大和
}seg[N<<2];

//求當下的區間連續最大和
void modify(Node &cur,Node &left,Node &right){
    cur.sum = left.sum+right.sum;
    cur.lmax = max(left.lmax,left.sum+right.lmax);
    cur.rmax = max(right.rmax,right.sum+left.rmax);
    cur.tmax = max(max(left.tmax,right.tmax),left.rmax+right.lmax);
}

//建立線段樹：[l,r)
void build(int l,int r,int cur){
    if(r<=l)return;
    if(r-l==1){
        seg[cur] = {arr[l],arr[l],arr[l],arr[l]};
        return;
    }
    int mid = (l+r)/2;
    build(l,mid,2*cur);
    build(mid,r,2*cur+1);
    modify(seg[cur],seg[2*cur],seg[2*cur+1]);
}

//區間詢問：[l,r)
Node query(int cur,int l,int r,int ql,int qr){
    if(r<=l || ql>=r || qr<=l)return {-INF,-INF,-INF,-INF};
    if(ql<=l && qr>=r)return seg[cur];
    int mid = (l+r)/2;
    auto left = query(2*cur,l,mid,ql,qr);
    auto right = query(2*cur+1,mid,r,ql,qr);
    Node temp;
    modify(temp,left,right);
    return temp;
}

signed main(){
    ios;
    cin>>n>>m;
    for(int i=1;i<=n;i++)cin>>arr[i];
    build(1,n+1,1);
    
    while(m--){
        int l,r;cin>>l>>r;
        auto ans = query(1,1,n+1,l,r+1);
        cout<<max(ans.tmax,(long long)0)<<endl;
    }
}
```

### d799區間求和
[題目連結](https://zerojudge.tw/ShowProblem?problemid=d799)
這一題要求兩個操作，區間加值跟區間查詢，這時候就必須用到**懶標(lazy tags)** 輔助求和。

> 範例測資：
> 10
> 1 2 3 4 5 6 7 8 9 10
> 3
> 2 2 4
> 1 3 6 3
> 2 2 4

![](https://i.imgur.com/MJMDQE9.jpg)
左邊是整棵線段樹，右邊則是懶標，可以看到3要往下推，但學長的程式碼中並沒有執行這個步驟，這樣會導致query的時候發生錯誤。

```cpp=
#include <iostream>
#include <string.h>
#include <stdio.h>
#define ios ios::sync_with_stdio(0),cin.tie(0);
#define N 500005
#define int long long
using namespace std;

struct Node{            //線段樹每一個節點
    int val=0,tag=0,sz; //val原本的值、tag懶標、sz區間大小
    int rv(){           //回傳實際節點的值
        return val+tag*sz;
    }
}seg[4*N];

int arr[N],n,m;

void build(int l,int r,int cur){    //建立線段樹
    seg[cur].sz = r-l;              //更新節點的大小
    if(r<=l)return;                 //空區間回傳
    if(r-l==1){                     //設定當前節點的值
        seg[cur].val = arr[l];
        return;
    }
    int m = (l+r)/2;                //分別遞迴建立左右子樹
    build(l,m,2*cur);
    build(m,r,2*cur+1);
    seg[cur].val = seg[2*cur].val+seg[2*cur+1].val;
}

//把id的懶標在query的時候往下推
void push(int id){
    seg[2*id].tag += seg[id].tag;   //左子樹懶標更新
    seg[2*id+1].tag += seg[id].tag; //右子樹懶標更新
    seg[id].val = seg[id].rv();     //更新實際值
    seg[id].tag = 0;                //往下推完之後設定為預設
}

//區間修改
void modify(int cur,int l,int r,int ql,int qr,int val){
    if (r<=l||ql>=r||qr<=l)return;
    if (ql<=l && qr>=r) {
        seg[cur].tag += val;               //將被完整包含的區間的懶標加上修改值
        return;
    }
    int mid = (l+r)/2;
    modify(cur*2,l,mid,ql,qr,val);          //修改左右子樹
    modify(cur*2+1,mid,r,ql,qr,val);        //遞迴完拉上來的過程中把上面的數值更新成有懶標
    seg[cur].val = seg[2*cur].rv()+seg[2*cur+1].rv();
}

//區間詢問
int query(int cur,int l,int r,int ql,int qr){
    if(r<=l || ql>=r || qr<=l)return 0;         //空集合直接回傳
    if(ql<=l && qr>=r)return seg[cur].rv();     //包含在要詢問的範圍中，回傳實際值
    push(cur);                                  //將懶標在遞迴下去的過程中下推
    int mid = (l+r)/2;                          //遞迴查詢左右子樹
    return query(cur*2,l,mid,ql,qr)+query(cur*2+1,mid,r,ql,qr);
}

signed main(){
    ios;
    cin>>n;
    for(int i=1;i<=n;i++)cin>>arr[i];
    build(1,n+1,1);
    cin>>m;
    
    while(m--){
        int p;cin>>p;
        if(p==1){
            int x,y,k;cin>>x>>y>>k;
            modify(1,1,n+1,x,y+1,k);
        }
        else if(p==2){
            int x,y;cin>>x>>y;
            int ans = query(1,1,n+1,x,y+1);
            cout<<ans<<endl;
        }
    }
}
```

### RMQ˙改
[題目連結](https://neoj.sprout.tw/problem/367/)
這一題是Range Maximum Query搭配區間加值，如果直接套用前面RMQ或是區間和的模板就輕鬆許多。這一題主要跟前面題目的不同在於它的sz要維護成1，有別於求區間和要乘上區間大小，每一個值不需要乘上區間大小，因此設成0。

這題第二筆測資明顯就是要卡暴力解法。
![](https://i.imgur.com/tC2Wucw.png)

```cpp=
#include <iostream>
#include <string.h>
#include <stdio.h>
#define ios ios::sync_with_stdio(0),cin.tie(0);
#define N 100005
#define int long long
using namespace std;
const int INF = 1e14;

struct Node{            //線段樹每一個節點
    int val=0,tag=0,sz=0; //val原本的值、tag懶標、sz區間大小
    int rv(){           //回傳實際節點的值
        return val+tag*sz;
    }
}seg[4*N];

int arr[N],n,m;

void build(int l,int r,int cur){    //建立線段樹
    seg[cur].sz = 1;                //更新節點的大小
    if(r<=l)return;                 //空區間回傳
    if(r-l==1){                     //設定當前節點的值
        seg[cur].val = arr[l];
        return;
    }
    int m = (l+r)/2;                //分別遞迴建立左右子樹
    build(l,m,2*cur);
    build(m,r,2*cur+1);
    seg[cur].val = max(seg[2*cur].val,seg[2*cur+1].val);
}

//把id的懶標在query的時候往下推
void push(int id){
    seg[2*id].tag += seg[id].tag;   //左子樹懶標更新
    seg[2*id+1].tag += seg[id].tag; //右子樹懶標更新
    seg[id].val = seg[id].rv();     //更新實際值
    seg[id].tag = 0;                //往下推完之後設定為預設
}

//區間修改
void modify(int cur,int l,int r,int ql,int qr,int val){
    if (r<=l||ql>=r||qr<=l)return;
    if (ql<=l && qr>=r) {
        seg[cur].tag += val;               //將被完整包含的區間的懶標加上修改值
        return;
    }
    int mid = (l+r)/2;
    modify(cur*2,l,mid,ql,qr,val);          //修改左右子樹
    modify(cur*2+1,mid,r,ql,qr,val);        //遞迴完拉上來的過程中把上面的數值更新成有懶標
    seg[cur].val = max(seg[2*cur].rv(),seg[2*cur+1].rv());
}

//區間詢問
int query(int cur,int l,int r,int ql,int qr){
    if(r<=l || ql>=r || qr<=l)return -INF;      //空集合直接回傳
    if(ql<=l && qr>=r)return seg[cur].rv();     //包含在要詢問的範圍中，回傳實際值
    push(cur);                                  //將懶標在遞迴下去的過程中下推
    int mid = (l+r)/2;                          //遞迴查詢左右子樹
    return max(query(cur*2,l,mid,ql,qr),query(cur*2+1,mid,r,ql,qr));
}

signed main(){
    ios;
    cin>>n>>m;
    for(int i=1;i<=n;i++)cin>>arr[i];
    build(1,n+1,1);
    
    while(m--){
        int p;cin>>p;
        if(p==1){
            int x,y,k;cin>>x>>y>>k;
            modify(1,1,n+1,x,y+1,k);
        }
        else if(p==2){
            int x,y;cin>>x>>y;
            int ans = query(1,1,n+1,x,y+1);
            cout<<ans<<endl;
        }
    }
}
```

### 1224 . 矩形覆蓋面積計算
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