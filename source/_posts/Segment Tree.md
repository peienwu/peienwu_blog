---
title: 2020資訊之芽—線段樹(Segment Tree)
date: 2021-8-5
tags: 
    - 2020資訊之芽
    - 2021暑假筆記
categories: 
    - C++進階主題
    - 線段樹

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
[懶標線段樹程式碼連結](https://gist.github.com/peienwu/368723520e3c39e4e0edc27a0823da69)，懶標這東西實在折磨人，學長寫的一個堪稱不用下推的懶標程式碼，結果他是錯的！！！害我Debug超久，可惡
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
