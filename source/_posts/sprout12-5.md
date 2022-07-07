---
title: "[題解]NEOJ 795 最近點對"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 最近點對
<!--more-->
[題目連結](https://neoj.sprout.tw/problem/795/)
大魔王題，害我TLE 27次。
二維平面，給定n個點，求出最近的兩個點的距離。
重大BUG害我卡三天的BUG在於，題目要求輸出距離的平方，結果在計算過程中就直接把距離當作是平方，自然有很多點會同時出現在一格網格座標中（我弱，一直TLE），還一直用什麼multi_map之類的，當然就是TLE嘍。
暴力做：$O(n^2)$，分治做：$O(nlogn)$，隨機做：期望$O(n)$,最慘$O(n^2)$
這一題的步驟：

1. 把 N 個點平移到第一象限
2. 把 N 個點的順序隨機打亂，d = dis(𝑎1, 𝑎2)
3. 以 𝑟 = 𝑑/2 的大小將二維平面切成網格狀，並將這些網格也 𝑥𝑦
以座標表示:點(𝑥,𝑦)會落入座標為 (𝑟,𝑟)
<font color="#f00">不會有兩個點在同一個格子內</font>（很重要的性質）
4. 一直把點加進網格中
如果產生新的最近點對一定會出現在以某個點為中心的5×5宮格內
5. 找到更近的最近點對
更新 r= 新的最近點對 /2 ，回到步驟三重來 O(i+1)

{% note info %}
**期望複雜度 $O(n)$ 證明**
![](https://i.imgur.com/S5dF8W7.png)

考慮加入第i+1個點時出現新的最近點對，發生的機率為：在$C_2^{i+1}$個配對中跟i+1個點產生最近點對共有i種可能，因此機率為$\frac{2}{i+1}$，當機率發生的時候，必須將所有的點都刪掉重新來一遍（因為r變小，unordered_map裡面的東西也要被清空，重新推入i+1個點），需要付出$O(i+1)$的時間，相乘起來每一個點期望的複雜度為$O(1)$，因此總時間複雜度為$O(n)$。
{% endnote %}

實作上可以用unordered_map，因為保證不會出現同一格的情況（一格間的最長距離也就不超過d，所以當出現在同一格就表示該更新了）

優化：先用$O(n^2)$的作法，找到前面的少數pair（$\sqrt n$）的最短距離，這樣可以減少被重新更新的機會
![](https://i.imgur.com/9XfZNH9.png)

[這篇文章](/N9zvIzP_Se-hpWZSaMv-sQ)有分治的作法，雖然分治的複雜度是O(n\log n)，隨機是$O(n)$，但因為隨機常數比較大的關係，時間比分治慢了快兩倍！下圖是分治的執行結果：
![](https://i.imgur.com/eCxfumY.png)

以下是隨機AC Code：

```cpp=
#include <bits/stdc++.h>
#define int long long int
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define N 200005
#define INF 1000000000LL
#define swift 1000000000
using namespace std;
int n,ans;
double r,d;
int dx[25] = {-2,-2,-2,-2,-2,-1,-1,-1,-1,-1,0,0,0,0,0,1,1,1,1,1,2,2,2,2,2};
int dy[25] = {-2,-1,0,1,2,-2,-1,0,1,2,-2,-1,0,1,2,-2,-1,0,1,2,-2,-1,0,1,2};
unordered_map<int, int> m;

void solve();
inline void init();
void solve();
bool insert(int,int,int);
inline double dis(int,int);
inline int Grid(int);

struct node{
    int x,y,ind;
}point[N];

//函式實作
inline void init(){
    m.clear();
}

inline int Grid(int ind){ //input網格座標
    int x = point[ind].x/r;
    int y = point[ind].y/r;
    return x*INF+y;
}
inline int dist(node a,node b){
    int x = a.x-b.x,y = a.y-b.y;
    return (x*x+y*y);
}

inline double dis(node a,node b){
    int x = a.x-b.x,y = a.y-b.y;
    return sqrt(x*x+y*y);
}

void solve(){
    m.insert(make_pair(Grid(0),0));m.insert(make_pair(Grid(1),1));
    for(int ind = 2;ind < n;ind++){
        int x = point[ind].x/r,y = point[ind].y/r,better=0;
        for(int i=0;i<25;i++){
            int nx = x+dx[i],ny = y+dy[i];
            auto it = m.find(nx*INF+ny);
            if(it!=m.end()){
                double distance = dis(point[it->second],point[ind]);
                if(distance<d){
                    better = 1;
                    ans = dist(point[it->second],point[ind]);
                    d = distance;
                    r = d/2;
                }
            }
        }
        if(better){    //前面的格子直接瑱入
            m.clear();
            for(int i=0;i<=ind;i++)m.insert(make_pair(Grid(i),i));
        }
        else{
            m.insert(make_pair(Grid(ind), ind));
        }
    }
}

signed main(){
    ios;
    init();
    cin>>n;
    for(int i=0;i<n;i++){
        int x,y;cin>>x>>y;
        x+=swift;y+=swift;
        point[i].x = x;point[i].y = y;
    }
    random_shuffle(point, point+n);
    int smalln = sqrt(n);
    ans = dist(point[0],point[1]);
    d = dis(point[0], point[1]);
    for(int i=0;i<=smalln;i++){
        for(int j=i+1;j<=smalln;j++){
            d = min(d,dis(point[i], point[j]));
            ans = min(ans,dist(point[i],point[j]));
        }
    }
    r = d/2;
    solve();
    cout<<ans<<endl;
}
```
