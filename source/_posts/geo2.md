---
title: 計算幾何例題（Computational Geometry Problems）
date: 2021-8-22
tags: 
    - 計算幾何
    - 暑期筆記
categories:
    - C++進階主題
    - 計算幾何
mathjax: true
---

## 題目目錄

- 向量加法
- 等長線段對
- 向左轉向右轉
- 線段相交
- 最小凸多邊形
- TIOJ 1178 Convex Hull
- 來吧，遊戲開始了
- 遊戲：最終回
- TIOJ 1205 直角三角形
<!--more-->
- TIOJ 1105 H.PS3
- ZJ b288: 夏季大三角
- TIOJ 1500 Clean up on aisle 3
- ZJ a871: Museum Area
- TIOJ 1280 領土 (Territory)
- TIOJ 1678 剪多邊形（molding）
- ZJ d269: 11579 - Triangle Trouble

### 向量加法

[題目連結](https://neoj.sprout.tw/problem/398/)
[Submission](https://neoj.sprout.tw/challenge/178462/)

> 題目敘述：
> 給你n個數字（0≤i<1，小數點精度到末九位），想知道到底有多少組 $(i,j,k)$ 滿足 $v_i+v_j=v_k$，其中 $i,j,k$ 可以重複。

這題其實跟計算幾何沒什麼關係，直接用unordered_map去做（有點像two sum，不過下面的code好像也不用開到multi），簡單！不過我在浮點數的地方吃了一些WA，最後算了直接改用字串處理這個惱人的東西XD

```cpp=
#include <bits/stdc++.h>
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define int long long
#define double long double
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define rep2(i,l,r) for(int i=l;i<r;i++)
#define pii pair<int,int>
#define x first
#define y second
#define eps (1e-9)
#define INF 1e10
#define N 2001
#define ll long long
#define ld long double
#define int long long
using namespace std;
int n;

signed main(){
    cin>>n;
    vector<int> vec(n);
    rep2(i,0,n){
        string s;cin>>s;
        int num = 0,times = 1000000000;
        for(int i=2;i<=10;i++){
            num += (s[i]-'0')*times;
            times/=10;
        }
        vec[i] = num;
    }
    unordered_multimap<int,int> mp;
    for(int i=0;i<n;i++){
        mp.insert({vec[i],i});
    }
    int ans = 0;
    for(int i=0;i<n;i++){
        for(int j=0;j<n;j++){
            ans += mp.count(vec[i] + vec[j]);
        }
    }
    cout<<ans<<endl;
}
```

### 等長線段對

[題目連結](https://neoj.sprout.tw/problem/399/)
[Submission](https://neoj.sprout.tw/challenge/178471/)
> 題目敘述：
> 給定平面上很多個點，求出有幾對線段等長（輸入有重複的點）。

既然n≤500，那就直接枚舉吧，沒啥特別難度。

```cpp=
#include <bits/stdc++.h>
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define rep(i,a,b) for(int i=a,i<=b;i++)
#define rep2(i,a,b) for(int i=a;i<b;i++)
#define pii pair<int,int>
#define pdd pair<double,double>
#define int long long
#define ll long long
#define ld long double
#define N 1003
#define eps 1e-9
#define x first
#define y second
using namespace std;
int n;
pii p[N];

int dist(pii a,pii b){
    int x = a.x-b.x,y = a.y-b.y;
    return x*x+y*y;
}

signed main(){
    Orz;
    cin>>n;
    rep2(i,0,n)cin>>p[i].x>>p[i].y;
    map<int,int>mp;
    for(int i=0;i<n;i++){
        for(int j=i+1;j<n;j++){
            int dis = dist(p[i],p[j]);
            mp[dis]+=1;
        }
    }
    int ans = 0;
    for(auto i:mp){
        int cnt = i.second;
        ans+=((cnt*(cnt-1))/2);
    }
    cout<<ans<<endl;
}
```

### 向左轉向右轉

[題目連結](https://neoj.sprout.tw/problem/400/)
[Submission](https://neoj.sprout.tw/challenge/178524/)

> 題目敘述
> 給你平面上n個點，依序走訪每一個點，試問走訪過程中共執行幾次的左轉、右轉以及迴轉。

很特別，計算幾何讓電腦可以處理平常我們所看到的平面圖形，可以利用向量內積、外積等方式判斷方向。這一題最重要的就是**方向函數**。傳入3個點$(A,B,O)$，方向函數會會回傳$\stackrel\longrightarrow{OA}\times \stackrel\longrightarrow{OB}$ 的正負數值。

下圖為外積$\stackrel\longrightarrow{OA}\times \stackrel\longrightarrow{OB}$ 的結果，當 $\sin\theta$的結果為負，也就是下圖的情況，從B走到A就需要往左邊走；反之亦然。
![](https://i.imgur.com/eDNRLhm.png)

至於如何判斷當兩個向量的方向呈現一直線時，也就是外積回傳的值為0時（$\sin\theta = 0$），應該是同向還是異向呢？這時候就需要搭配向量內積（這我想了很久），因為內積公式是$A\cdot B = |A||B|\cos\theta$，將兩個向量內積之後就可以很明確的判斷到底是朝原本的方向走，還是反方向的行走！

{% note info %}
**內積、外積公式**
有一點數學，不過蠻有趣的。可以利用$\sin$與$\cos$達到計算角度的目的，利用兩者不同的值域，互相搭配，就可以更輕鬆的進行判斷！注意到外積的正負就代表著A到B是順時針或是逆時鐘。
$$A\cdot B = |A||B|\cos\theta = A_xB_x+A_yB_y\\A\times B = |A||B|\sin\theta = A_xB_y-A_yB_x$$

<br>

**方向函數**

當我們要判斷方向的時候，會利用正弦函數，逆時針正、順時針為負進行判斷！

```cpp=
int dir(pt a, pt b, pt o) {
    int cross = (a - o) ^ (b - o);
    if(fabs(cross) <= eps) return 0;
    else if(cross > 0) return 1;
    else return -1;
}
```

注意到此時在判斷是否為平行的時候（cross==0），使用到$fabs()$這個函數，目的是為了避免誤差而導致判斷錯誤，因此需要進行誤差的處理（其實不用也沒差啦，只是這樣嚴謹一點）
{% endnote %}

以下是AC Code：

```cpp=
#include <bits/stdc++.h>
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define rep(i,a,b) for(int i=a;i<=b;i++)
#define pii pair<int,int>
#define pdd pair<double,double>
#define int long long
#define ll long long
#define ld long double
#define N 1003
#define eps 1e-9
#define x first
#define y second

using namespace std;

struct pt{
    int x,y;
    bool operator < (pt b){
        if(x == b.x)return y < b.y;
        return x < b.x;
    }
    bool operator > (pt b){
        if(x == b.x)return y > b.y;
        return x > b.x;
    }
    bool operator == (pt b){
        if(x-b.x<=eps && y-b.y<=eps)return true;
        return false;
    }
    pt operator+(pt b) {return {x + b.x, y + b.y};} //向量相加
    pt operator-(pt b) {return {x - b.x, y - b.y};} //向量相減
    int operator^(pt b) {return x * b.y - y * b.x;} //向量外積cross
    int operator*(pt b) {return x * b.x + y * b.y;} //向量內積dot
};
vector<pt> a;
int dir(pt a, pt b, pt o) {
    int cross = (a - o) ^ (b - o);
    if(fabs(cross) <= eps) return 0;
    else if(cross > 0) return 1;
    else return -1;
}

int n,t;

signed main(){
    Orz;
    cin>>n;
    a.resize(n+2);
    rep(i,1,n)cin>>a[i].x>>a[i].y;
    int right = 0,left = 0,turn = 0;
    pt pre = a[1],from = a[2];
    for(int i=3;i<=n;i++){
        int ori = dir(a[i],from,pre);
        if(ori == 1)right+=1;
        else if(ori == -1)left+=1;
        else if(ori == 0 && ((a[i]-from)*(from-pre))<0)turn+=1;
        pre = from;from = a[i];
    }
    cout<<left<<" "<<right<<" "<<turn<<endl;
}
```

### 線段相交

[題目連結](https://neoj.sprout.tw/problem/401/)
[Submission](https://neoj.sprout.tw/challenge/178537/)
線段相交 = ~~線段香蕉~~，自動選字永遠都是香蕉，有點煩XDD

如何判斷兩線段是否相交？首先需要一個函數可以判斷點是否在一個線段上，如此一來就可以判斷端點在另一條線段上的特殊情況。以下程式碼為判斷點$P_o$ 是否在 $\overline{P_aP_b}$ 上。利用向量外積可以判斷兩線段是否平行，而使用內積公式可以判斷$P_o$是否在線段中，而非線段的兩側！

```cpp=
bool onseg(pt a, pt b, pt o){       //o是否在ab線段上
    int cross = (a - o) ^ (b - o);  //是否平行
    int dot = (a - o) * (b - o);    //是否在線段中
    return (cross == 0)&&(dot <= 0);
}
```

說明：由點$P_o$指向a和b的向量必須呈現180度角（也就是異向），才可確保在ab線段中（跟a,b重合也算是跟ab線段相交）。

接下來是主要的部分，首先先確認4個端點是否恰好在另外一條線段上，判斷完之後就是處理一般相交的情況。若線段 $\overline{P_1P_2}$ 與 $\overline{P_3P_4}$ 相交，則點 $P_1$ 與點 $P_2$ 會在線段$\overline{P_3P_4}$ 的異側。用方向函數表示：$dir(a,b,c)\times dir(a,b,d)<0$。確認完兩個線段之後即完成線段相交的判斷！

```cpp=
bool Intersection(pt a, pt b, pt c, pt d){      //線段ab是否與cd相交
    if(onseg(a,b,c)||onseg(a,b,d))return true;  //點c、d是否洽在線段ab上
    if(onseg(c,d,a)||onseg(c,d,b))return true;  //點a、b是否洽在線段cd上
    if(dir(a,b,c)*dir(a,b,d)==-1 && dir(c,d,a)*dir(c,d,b)==-1)
        return true;                            //對於線段兩端點看另外兩端點必須方向相反
    return false;
}
```

由下圖可以得到上面的結論，當兩線段相交時，方向函數得到的值（用外積，也就是下圖 $\theta_1$ 以及 $\theta_2$）的方向），會呈現一正一負，從兩個相反的方向看同一條線段得出來的結論！
![](https://i.imgur.com/b5pW6IS.png)

AC Code:

```cpp=
#include <bits/stdc++.h>
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define rep(i,a,b) for(int i=a;i<=b;i++)
#define pii pair<int,int>
#define pdd pair<double,double>
#define int long long
#define ll long long
#define ld long double
#define N 1003
#define eps 1e-9
#define x first
#define y second

using namespace std;

struct pt{
    int x,y;
    bool operator < (pt b){
        if(x == b.x)return y < b.y;
        return x < b.x;
    }
    bool operator > (pt b){
        if(x == b.x)return y > b.y;
        return x > b.x;
    }
    bool operator == (pt b){
        if(x-b.x<=eps && y-b.y<=eps)return true;
        return false;
    }
    pt operator+(pt b) {return {x + b.x, y + b.y};} //向量相加
    pt operator-(pt b) {return {x - b.x, y - b.y};} //向量相減
    int operator^(pt b) {return x * b.y - y * b.x;} //向量外積cross
    int operator*(pt b) {return x * b.x + y * b.y;} //向量內積dot
};
vector<pt> point;
int dir(pt a, pt b, pt o) {                         //方向函數
    int cross = (a - o) ^ (b - o);
    if(fabs(cross) <= eps) return 0;
    else if(cross > 0) return 1;
    else return -1;
}
bool onseg(pt a, pt b, pt o){                       //o是否在ab線段上
    int cross = (a - o) ^ (b - o);                  //是否平行
    int dot = (a - o) * (b - o);                    //是否在線段中
    return (cross == 0)&&(dot <= 0);
}

bool Intersection(pt a, pt b, pt c, pt d){          //線段ab是否與cd相交
    if(onseg(a,b,c)||onseg(a,b,d))return true;      //點c、d是否洽在線段ab上
    if(onseg(c,d,a)||onseg(c,d,b))return true;      //點a、b是否洽在線段cd上
    if(dir(a,b,c)*dir(a,b,d)==-1 && dir(c,d,a)*dir(c,d,b)==-1)
        return true;                                //對於線段兩端點看另外兩端點必須方向相反
    return false;
}
int n,t;

void solve(){
    point.assign(4,{0,0});
    rep(i,0,3)cin>>point[i].x>>point[i].y;
    if(Intersection(point[0],point[1],point[2],point[3])){
        cout<<"Yes"<<endl;
    }
    else cout<<"No"<<endl;
}

signed main(){
    Orz;
    int t;cin>>t;
    while(t--){
        solve();
    }
}
```

### TIOJ 1178 Convex Hull

[題目連結](https://tioj.ck.tp.edu.tw/problems/1178)
[Submission](https://tioj.ck.tp.edu.tw/submissions/262532)

> 題目敘述
給定n個二維平面的點，找出位在凸包上的所有點的個數

最小凸多邊形 = 凸包，要找出能包住所有點的最小凸多邊形，簡稱凸包。聽說最好寫的凸包演算法是：Andrew's Monotone Chain，翻成中文叫做Andrew's 單調鍊？有一點單調+鍊的味道。下圖是我用照片合成起來的GIF，大致模擬出使用Andrew's Monotone Chain 找凸包的方法。

![](https://i.imgur.com/YUOC9xZ.gif)

{% note default %}
**Andrew's Monotone Chain**
這個演算法的時間複雜度是 $O(n\log n)$，空間複雜度 $O(n)$，資料說它可以解決了凸包有重疊的點、共線的點、退化成線段和點的情況。它的名字叫做「單調鍊」，要維護一個有點像單調隊列的東西，對於在容器中第 $i$ 個位置的點都滿足 $\stackrel\longrightarrow{P_i P_{i+1}}\times \stackrel\longrightarrow{P_{i+1} P_{i+2}} > 0$ ，如果有點做外積後的結果小於等於0，則它會被pop掉（這是依照上圖逆時針完成凸包的描述，如果方向相反則會變號）。
<br>

以下是此演算法的執行步驟：

1. 先把所有的點按照 $(x,y)$ 排序
2. 將下凸包圍出來，有點像維護單調隊列，對所有新加入的點i計算點i-2、i-1與i之間的外積，如果不符合情況代表圍不到新加入的點，需要將舊的點pop出來
3. 將原本已經排序好的點逆序
3. 再把上凸包由x座標大到小圍出來，將上下合併就是凸包了（必須注意起終點被push的次數問題）！
{% endnote %}

一般會用一個vector儲存在凸包上面的點（不包含在邊上的點，只有位於轉折點的點），在頭尾的部分（x座標最大與最小）需要特別處理，讓每一個點最多近到vector一次。

{% note success %}
**實作細節**
以下是確認是否需要將vector中元素pop出來的關鍵，對向量$\stackrel\longrightarrow{OA}\times \stackrel\longrightarrow{OB}$ 做外積的結果，必須排除外積結果為0的情況，如果將0也納入，會造成一個點被push進去很多次，在數量和計算上出現問題。

```cpp=
bool check(pt a,pt b,pt o){
    int cross = (a - o)^(b - o);
    return cross >= 0;         //這裡很關鍵，別吃WA
}
```

除此之外，上凸包在範圍限制上是需要注意的。假設x座標最大的點i，當在圍上凸包的過程中i是不可以被pop出去的，因此vector的大小必須大於下凸包的大小。

凸包使用第i-1跟第i個點的向量去看第i到第i+1個點的向量，決定一個點要不要被推入vector中。當我們逆序從x座標最大的點往前看時，要確保每一輪結束之後在i點後都必須要有至少一個點，設定hull.size() > down_hull的原因是防止在下凸包的點被圍上凸包的過程更新到。

```cpp=+
int down_hull = hull.size();    //圍上凸包的程式碼片段
for(auto i: p){
    while(hull.size() > down_hull 
        && check(i,hull[hull.size()-1],hull[hull.size()-2])){
        hull.pop_back();
    }
    hull.push_back(i);
}
```

{% endnote %}
以下是AC Code:

```cpp=
#include <bits/stdc++.h>
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define rep(i,a,b) for(int i=a;i<=b;i++)
#define pii pair<int,int>
#define pdd pair<double,double>
#define int long long
#define ll long long
#define ld long double
#define N 100001
#define eps 1e-9
#define x first
#define y second

using namespace std;

struct pt{
    int x,y;
    bool operator < (pt b){
        if(x == b.x)return y < b.y;
        return x < b.x;
    }
    bool operator > (pt b){
        if(x == b.x)return y > b.y;
        return x > b.x;
    }
    bool operator == (pt b){
        if(x-b.x<=eps && y-b.y<=eps)return true;
        return false;
    }
    pt operator+(pt b) {return {x + b.x, y + b.y};} //向量相加
    pt operator-(pt b) {return {x - b.x, y - b.y};} //向量相減
    int operator^(pt b) {return x * b.y - y * b.x;} //向量外積cross
    int operator*(pt b) {return x * b.x + y * b.y;} //向量內積dot
};
bool cmp(pt a, pt b){
    if(a.x == b.x)return a.y < b.y;
    return a.x < b.x;
}
vector<pt> p;

bool check(pt a,pt b,pt o){
    int cross = (a - o)^(b - o);
    return cross >= 0;                              //這裡很關鍵，別吃WA
}

int n,t;

vector<pt> convex_hull(){
    vector<pt> hull;
    sort(p.begin(),p.end(),cmp);       //首先對x進行排序
    for(auto i : p){                   //依序走訪，如果遇到外積<0則不在凸包上
        while(hull.size()>=2 && check(i,hull[hull.size()-1],hull[hull.size()-2])){
            hull.pop_back();
        }
        hull.push_back(i);             //在凸包hull的每一點都符合外積小於0
    }
    int down_hull = hull.size();
    hull.pop_back();                   //x最大的點會在凸包上，不用做兩次先pop一次
    reverse(p.begin(),p.end());        //將所有點逆序之後做一次上面的凸包
    for(auto i: p){
        while(hull.size() > down_hull && check(i,hull[hull.size()-1],hull[hull.size()-2])){
            hull.pop_back();
        }
        hull.push_back(i);
    }
    return hull;                       //起點會經過兩次，剛好來算有向面積
}

signed main(){
    Orz;
    cin>>n;
    p.assign(n,{0,0});
    rep(i,0,n-1)cin>>p[i].x>>p[i].y;
    vector<pt> hull = convex_hull();
    cout<<hull.size()-1<<endl;
}
```

### 最小凸多邊形

[題目連結](https://neoj.sprout.tw/problem/402/)
[Submission](https://neoj.sprout.tw/challenge/178589/)

> 題目敘述
找出二維平面上n個點的凸包所圍出來的面積為何？

跟上一題類似，在找到全部在凸包上面的點後，就可以利用**有向面積**把凸包面積算出來，有一個公式可以計算多邊形面積，利用外積得到正負值，轉一圈後得到面積！對於多邊形的頂點 $P_0,P_1,...,P_{n-1},P_n=P_0$ 的面積如下：
$$Area = \frac{1}{2}\sum_{i=0}^{n-1}\stackrel\longrightarrow{P_i}\times \stackrel\longrightarrow{P_{i+1}}$$

其中最後一個點會回到起點，形成一個封閉的迴路。

以下是AC Code：

```cpp=
#include <bits/stdc++.h>
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define rep(i,a,b) for(int i=a;i<=b;i++)
#define pii pair<int,int>
#define pdd pair<double,double>
#define int long long
#define ll long long
#define ld long double
#define N 100001
#define eps 1e-9
#define x first
#define y second

using namespace std;

struct pt{
    int x,y;
    bool operator < (pt b){
        if(x == b.x)return y < b.y;
        return x < b.x;
    }
    bool operator > (pt b){
        if(x == b.x)return y > b.y;
        return x > b.x;
    }
    bool operator == (pt b){
        if(x-b.x<=eps && y-b.y<=eps)return true;
        return false;
    }
    pt operator+(pt b) {return {x + b.x, y + b.y};} //向量相加
    pt operator-(pt b) {return {x - b.x, y - b.y};} //向量相減
    int operator^(pt b) {return x * b.y - y * b.x;} //向量外積cross
    int operator*(pt b) {return x * b.x + y * b.y;} //向量內積dot
};
bool cmp(pt a, pt b){
    if(a.x == b.x)return a.y < b.y;
    return a.x < b.x;
}

vector<pt> p;

bool check(pt a,pt b,pt o){
    int cross = (a - o)^(b - o);
    return cross >= 0;
}

int n,t;

vector<pt> convex_hull(){
    vector<pt> hull;
    sort(p.begin(),p.end(),cmp);       //首先對x進行排序
    for(auto i : p){                   //依序走訪，如果遇到外積<0則不在凸包上
        while(hull.size() > 1 && check(i,hull[hull.size()-1],hull[hull.size()-2])){
            hull.pop_back();
        }
        hull.push_back(i);             //在凸包hull的每一點都符合外積小於0
    }
    int down_hull = hull.size();
    hull.pop_back();                   //x最大的點會在凸包上，不用做兩次先pop一次
    reverse(p.begin(),p.end());        //將所有點逆序之後做一次上面的凸包
    for(auto i: p){
        while(hull.size() > down_hull && check(i,hull[hull.size()-1],hull[hull.size()-2])){
            hull.pop_back();
        }
        hull.push_back(i);
    }
    return hull;                       //起點會經過兩次，剛好來算有向面積
}

signed main(){
    Orz;
    cin>>t;
    while(t--){
        cin>>n;
        p.assign(n,{0,0});
        rep(i,0,n-1)cin>>p[i].x>>p[i].y;
        vector<pt> hull = convex_hull();
        int area = 0,len = hull.size();
        for(int i=0;i<len-1;i++)area += (hull[i]^hull[i+1]);
        cout<<fixed<<setprecision(1)<<((ld)area/2)<<endl;
    }
}
```

### 來吧，遊戲開始了

[題目連結](https://neoj.sprout.tw/problem/790/)
[Submission](https://neoj.sprout.tw/challenge/178691/)
[GGB模擬](https://www.geogebra.org/graphing/h4fxdquw)

> 題目敘述
給你二維平面上n個點(n≤2400)，每一個點座標皆不相同，求出總共可以圍出多少個三角形？

這是NEOJ上的加分題，好像是一個題組吧，反正總共有三題，這是第一題。如果 $O(n^3)$ 的枚舉，複雜度會爆炸（量級約$10^{10}$），根據電神的說法，這一題要用極角排序以及雙指標找到共線，接著就可以利用排列組合把因為共線而不能形成三角形的組合扣掉，就是答案了。

![](https://i.imgur.com/t5TF96I.png)

這一題的核心概念是找共線，具體來說的作法是枚舉每一個點的同時，以它為原點對其他的點進行排序，如果遇到有相同的極角座標表示這些點共線，同時利用陣列cnt[x]統計共線點數為x的線段總共有幾條。

以下的GIF就是大致上程式執行的樣子。因為一條長度為x的線段會因為枚舉x次的關係，在最後扣掉的情況會重複x次因此需要除掉。

![](https://i.imgur.com/MVfEx4i.gif)

{% note primary %}
**共線與三角形**

一般情況下（任三點不共線），總共可以形成 $C^n_3$ 個三角形，如果有一條m個點共線的情況下（其他點不共線），則可以形成的三角形數量就必須扣除共線限制的情況，變成 $C^n_3-C^m_3$ 個三角形。
{% endnote %}

時間複雜度為：枚舉每一個點 $O(n)$，極角排序 $O(n\log n)$，總時間複雜度 $O(n^2\log n)$

{% note default %}
**實作小細節**

**1. 維護共線連續區間**

我們要想辦法讓有共線的點們所在位置是一個連續的位置。三個點共線可能為在對角線的象限中，也就是點差了180度，如此一來就沒辦法讓共線的點為在連續的區間。為了達到這個目的，我們將所有位於下半平面的點都移到上半平面（在上半平面找到有相同 $\tan\theta$ 值的位置），接著就能利用雙指針找極角座標排序後有相同極角的區間之最大值！

**2. 特例判斷**

如果有一點y座標為0但x座標為負，要將其移到x軸正向的地方，不能把這種情況涵蓋為一般情況，否則原本在x軸正向的點會被移到x軸負向，沒有達到預期的效果。
{% endnote %}

以下是AC Code：

```cpp=
#include <bits/stdc++.h>
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define rep(i,a,b) for(int i=a;i<=b;i++)
#define pii pair<int,int>
#define pdd pair<double,double>
#define int long long
#define ll long long
#define ld long double
#define N 100001
#define all(x) x.begin(),x.end()
#define eps 1e-9
#define x first
#define y second

using namespace std;

struct pt{
    int x,y;
    bool operator < (pt b){
        if(x == b.x)return y < b.y;
        return x < b.x;
    }
    bool operator > (pt b){
        if(x == b.x)return y > b.y;
        return x > b.x;
    }
    bool operator == (pt b){
        if(x-b.x == 0 && y-b.y == 0)return true;
        return false;
    }
    pt operator+(pt b) {return {x + b.x, y + b.y};} //向量相加
    pt operator-(pt b) {return {x - b.x, y - b.y};} //向量相減
    int operator^(pt b) {return x * b.y - y * b.x;} //向量外積cross
    int operator*(pt b) {return x * b.x + y * b.y;} //向量內積dot
};

vector<pt> p;
vector<int> cnt;
int n,ans = 0;

bool cmp(pt a, pt b){
    bool f1 = a < pt{0,0};
    bool f2 = b < pt{0,0};
    if(f1 != f2)return f1 < f2;
    return (a ^ b) > 0;
    //逆時針將點進行極角排序，從270度開始逆時針
}

//用cnt[i]統計區間長度為i的線段數量
void solve(pt id){
    vector<pt> pp;
    for(auto i : p){                         //以id為原點
        pt cur = i-id;
        if(cur == pt{0,0})continue;
        if(cur.y < 0){cur.x = -cur.x;cur.y = -cur.y;}
        if(cur.x < 0 && cur.y==0){cur.x = -cur.x;}
        pp.push_back(cur);
    }
    sort(all(pp),cmp);                      //將id當作原點進行排序
    int p1 = 0,p2 = 0,len = pp.size();      //雙指針找共線區間
    while(p1 < n-1){                        //最大化區間
        while(p2+1 < len && (pp[p1]^pp[p2+1]) == 0)p2++;
        cnt[p2-p1+2]+=1;
        p1 = p2+1;
    }
}

signed main(){
    Orz;
    cin>>n;
    p.assign(n,{0,0});
    cnt.resize(n+1,0);
    rep(i,0,n-1)cin>>p[i].x>>p[i].y;
    rep(i,0,n-1)solve(p[i]);
    int ans = (n*(n-1)*(n-2))/6;
    rep(i,3,n)ans-=(cnt[i]*(i-1)*(i-2))/6;
    cout<<ans<<endl;
}
```

### 遊戲：最終回

[題目連結](https://neoj.sprout.tw/problem/792/)
[Submission](https://neoj.sprout.tw/challenge/178786/)
> 題目敘述
共有n個二維平面上的格子點，這些點會形成簡單多邊形。試求或在簡單多邊形內部的格線總長（包括垂直與水平格線）。

這邊有一個不嚴謹的推導方式，不過他是正確的。令多邊形內部格線長度為S，多邊形的邊落在的格線長度為T，多邊形面積T，則有以下關係式：

$$S = 2A-\frac{T}{2}$$

詳細的公式推導可以可以參閱下圖，平行四邊形（斜線部分）內部**垂直**的格線長度為： 大矩形 $(x_1+x_2)(y_1+y_2)$ 扣掉左右上下共四個三角形兩兩拼成一個矩形 $x_1y_1$ 以及 $x_2y_2$，還有左上右下兩個正方形 $2x_2y_1$，整理之後會發現其實跟面積是一樣的。對於垂直部分也是類似的情況。

![](https://i.imgur.com/wemDPMp.jpg)

好像隱約發現到面積與格線長度有十分密切的關係，算出面積，把在格線上的邊進行特判扣掉，就可以得到格線長度。

這一題我想了很久，一直看不出來關係式到底長怎樣，直到大神提點才發現原來有這樣的關係，我反應好遲鈍:cry:

![](https://i.imgur.com/Fy1wSky.png)

以下是AC Code：

```cpp=
#include <bits/stdc++.h>
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define rep(i,a,b) for(int i=a;i<=b;i++)
#define pii pair<int,int>
#define pdd pair<double,double>
#define int long long
#define ll long long
#define ld long double
#define N 100001
#define all(x) x.begin(),x.end()
#define eps 1e-9
#define x first
#define y second

using namespace std;

struct pt{
    int x,y;
    bool operator < (pt b){
        if(x == b.x)return y < b.y;
        return x < b.x;
    }
    bool operator > (pt b){
        if(x == b.x)return y > b.y;
        return x > b.x;
    }
    bool operator == (pt b){
        if(x-b.x == 0 && y-b.y == 0)return true;
        return false;
    }
    pt operator+(pt b) {return {x + b.x, y + b.y};} //向量相加
    pt operator-(pt b) {return {x - b.x, y - b.y};} //向量相減
    int operator^(pt b) {return x * b.y - y * b.x;} //向量外積cross
    int operator*(pt b) {return x * b.x + y * b.y;} //向量內積dot
};

vector<pt> p;
vector<int>  cnt;
int n,edge,ans;

int solve(){
    int area = 0;
    rep(i,0,n-1){
        area += (p[i]^p[i+1]);
        if(p[i].y == p[i+1].y)edge += abs(p[i].x-p[i+1].x);
        if(p[i].x == p[i+1].x)edge += abs(p[i].y-p[i+1].y);
    }
    area = abs(area);
    return area;
}

signed main(){
    Orz;
    cout<<fixed<<setprecision(1);
    while(cin>>n){
        p.assign(n+1,{0,0});
        edge = 0;
        rep(i,0,n-1)cin>>p[i].x>>p[i].y;
        p[n] = p[0];
        ans = solve();
        cout<<ans-((ld)edge/2)<<endl;
    }
}
```

### TIOJ 1205 直角三角形

[題目連結](https://tioj.ck.tp.edu.tw/problems/1205)
[Submission](https://tioj.ck.tp.edu.tw/submissions/262842)

> 題目敘述
給你N（N≤1500）個座標平面上的點，請問總共可形成多少個直角三角形呢？

從極角排序後的第一個點開始逆時針進行雙指針的枚舉。這邊使用到一個很特別的手法，對於共線的情況我們先透過預處理的方式將共線的點合併起來，並用cnt[x]陣列紀錄第x個點是由幾個點所合併起來的，如此一來，在進行計算的時候就不會有共線要分別處理的問題（不需擔心是不是可以跟之前的點形成直角三角形，因為相同斜率的點已經被合併剩下一個），直接將數量相乘就可以知道直角三角形的數量！

時間複雜度：枚舉所有點 $O(n)\times$ 進行極角排序$O(n\log n)$ 以及雙指標$O(n)$，總時間複雜度為 $O(n^2\log n)$。

{% note info %}
**實作小細節**

雙指針進行枚舉的過程中，很有可能會指標指向的索引值會超出範圍。解決的方法有兩種：

1. 超出了即代表繞了一圈，只需要對索引值取餘數即可。
2. 除了取餘數的方法之外，其實也可以直接在點集後面將所有點再推入一次，讓角度從360延伸成720度，就不會有超出範圍的問題！
{% endnote %}

以下是AC Code：

```cpp=
#include <bits/stdc++.h>
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define rep(i,a,b) for(int i=a;i<=b;i++)
#define pii pair<int,int>
#define pdd pair<double,double>
#define int long long
#define ll long long
#define ld long double
#define N 100001
#define all(x) x.begin(),x.end()
#define eps 1e-9
#define x first
#define y second

using namespace std;

struct pt{
    int x,y;
    bool operator < (pt b){
        if(x == b.x)return y < b.y;
        return x < b.x;
    }
    bool operator > (pt b){
        if(x == b.x)return y > b.y;
        return x > b.x;
    }
    bool operator == (pt b){
        if(x-b.x == 0 && y-b.y == 0)return true;
        return false;
    }
    pt operator+(pt b) {return {x + b.x, y + b.y};} //向量相加
    pt operator-(pt b) {return {x - b.x, y - b.y};} //向量相減
    int operator^(pt b) {return x * b.y - y * b.x;} //向量外積cross
    int operator*(pt b) {return x * b.x + y * b.y;} //向量內積dot
};

vector<pt> p,temp,pp;
vector<int>  cnt;
int n,ans = 0;

bool cmp(pt a, pt b){
    bool f1 = a < pt{0,0};
    bool f2 = b < pt{0,0};
    if(f1 != f2)return f1 < f2;
    return (a ^ b) > 0;
    //逆時針將點進行極角排序，從270度開始逆時針
}

//O(n)枚舉每個點當直角情況
int solve(pt id){
    pp.clear();cnt.clear();temp.clear();
    for(pt i : p){
        pt cur = i - id;
        if(cur == pt{0,0})continue;
        temp.push_back(cur);
    }
    sort(all(temp),cmp);            //以id為原點進行極角排序
    pp.push_back(temp[0]);          //pp每一角度只存至多一個點
    cnt.push_back(1);               //考慮每個點共線情況
    int len = temp.size();
    rep(i,1,len-1){
        int cross = temp[i]^temp[i-1],dot = temp[i]*temp[i-1];
        if(cross == 0 && dot >= 0)cnt[cnt.size()-1] += 1;   //共線數量+=1
        else {pp.push_back(temp[i]);cnt.push_back(1);}      //非共線設定數量為1
    }
    len = pp.size();            //考慮橫跨一周的情況
    rep(i,0,len-1){             //雙指針i,p1可能會超過一圈
        pp.push_back(pp[i]);    //將點再繞一圈
        cnt.push_back(cnt[i]);
    }
    int ans = 0,p1 = 0;
    rep(i, 0, len-1){
        while(p1 < i+len && (pp[i]^pp[p1]) >= 0 && (pp[i]*pp[p1]) > 0)p1 += 1;
        //夾銳角的情況要p1+=1
        if((pp[i]^pp[p1]) > 0 && (pp[i]*pp[p1]) == 0)ans += cnt[i]*cnt[p1];
        //正向的直角三角形，若共線則兩者數量相乘
    }
    return ans;
}

signed main(){
    Orz;
    while(cin>>n){
        if(n == 0)break;
        p.assign(n,{0,0});
        rep(i,0,n-1)cin>>p[i].x>>p[i].y;
        
        int ans = 0;
        rep(i,0,n-1){
            ans += solve(p[i]);
        }
        cout<<ans<<endl;
    }
}
```

### TIOJ 1105 H.PS3

[題目連結](https://tioj.ck.tp.edu.tw/problems/1105)
[Submission $O(n^2)$](https://tioj.ck.tp.edu.tw/submissions/262930)
[Submission $O(n\log n)$](https://tioj.ck.tp.edu.tw/submissions/262947)

> 題目敘述
給你平面上N個點（N≤3000），請求出最遠點對的索引值（小的在前、大的在後）

我做了一份[最近點對：不同複雜度之解決方式](/hVplrqxCRdGiMkn2lwXGAA)的筆記，共有四種方法可以解決那個問題，這一題要求的是最遠點對，作法與最近點對其實差蠻遠的。由上幾題知道凸包的求法，因為凸包是可以圍住所有點的多邊形，因此最遠點對也應該在凸包上，而且所在的位置會為在凸包的兩側上（如果不落在凸包上，一定可以把點向兩側延伸到凸包上，且移動過後的點對距離一定比原始的點對距離大）。

找完凸包之後，可以用旋轉卡尺的方式尋找最遠點對。想像兩條平行線中間夾著凸包，逆時鐘旋轉繞行凸包一圈，過程不斷更新最遠點對的距離。在實作上兩條平行線可以被想像成由 $P_i$ 指向 $P_{i+1}$ 的向量，透過外積三角形面積公式決定卡尺該如何移動。

$$AREA = |\overrightarrow{AB}\times \overrightarrow{AC}|$$

以下圖為例，我們要找 $\overline{HM}$ 為底可以形成的最大三角形面積的頂點，因為在同底的情況下面積就代表點與邊的垂直距離，最大的垂直距離意味著這條底邊可以垂直延伸的最遠距離。因為凸包必定是凸多邊形，因此三角形的面積會呈現單峰函數，因此只需要從下一個三角形面積的大小，決定雙指針中比較快的指標的移動情況。

![](https://i.imgur.com/SXv0gfN.png)

如果仔細來看，以下圖為例，當前較快的指標指向的位置是 $D$ 點，考慮一條與與 $\overline{HM}$ 平行的直線，若下一個點 $J$ 在平行線段的另外一側，則將指標移往 $J$ 點。可能會有一個疑問，如果比較下圖的線段長度，會發現到 $\overline{DH}$ 的長度比經過 $J$ 點的兩條線段都還要長，那為何還要更新至 $J$ 點？舉這個例子不太好，不過可以想像當旋轉卡尺轉到以 $\overline{FH}$ 為底的時候，會將最遠點對的距離更新成 $\overline{HD}$ 的長度。如果今天 $H$ 的左側又多加了一個新點 $P$，則最遠點對會變成 $\overline{PD}$ 的距離。

簡單來說，最遠點對一定會發生對角的凸包點上面，即使現在以 $\overline{HM}$ 為底最遠點並非 $J$ 而是 $D$ ，但在旋轉卡尺旋轉到 $\overline{FH}$ 時就能將距離更新成 $\overline{HD}$ 的距離。

![](https://i.imgur.com/6Jeg2U8.png)

{% note success %}
**實作小細節**

這一題有點麻煩，因為他要輸出的是最遠點對的索引值，而不是最遠點對之間的距離。在尋找凸包的過程中，會對所有點進行排序，因此原有的索引值順序會被打亂，需要在一開始輸入的時後就好好維護每一個座標的索引值。
{% endnote %}

以下是AC Code：

```cpp=
#include <bits/stdc++.h>
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define rep(i,a,b) for(int i=a;i<=b;i++)
#define pii pair<int,int>
#define pdd pair<double,double>
#define int long long
#define ll long long
#define ld long double
#define N 100001
#define all(x) x.begin(),x.end()
#define eps 1e-9
#define x first
#define y second

using namespace std;

struct pt{
    int x,y,ind;
    bool operator < (pt b){
        if(x == b.x)return y < b.y;
        return x < b.x;
    }
    bool operator > (pt b){
        if(x == b.x)return y > b.y;
        return x > b.x;
    }
    bool operator == (pt b){
        if(x-b.x == 0 && y-b.y == 0)return true;
        return false;
    }
    pt operator+(pt b) {return {x + b.x, y + b.y};} //向量相加
    pt operator-(pt b) {return {x - b.x, y - b.y};} //向量相減
    int operator^(pt b) {return x * b.y - y * b.x;} //向量外積cross
    int operator*(pt b) {return x * b.x + y * b.y;} //向量內積dot
    int dis() {return x*x + y*y;}
};

vector<pt> p,hull;
pt pt_ans;
int n,h;

bool check(pt a, pt b, pt o){
    pt aa = a - o;
    pt bb = b - o;
    return (aa ^ bb) >= 0;
}

bool check2(pt a,pt b,pt c,pt d){
    int aa = abs((a - c)^(b - c));
    int bb = abs((a - d)^(b - d));
    return aa < bb;
}

bool cmp(pt a, pt b){
    if(a == b)return a.ind < b.ind;
    if(a.x == b.x)return a.y < b.y;
    return a.x < b.x;
}

void convex_hull(){
    stable_sort(all(p),cmp);
    rep(i,0,n-2)if(p[i] == p[i+1])p[i+1].ind = p[i].ind;
    hull.clear();
    for(auto i : p){
        while(hull.size() > 1 && check(i,hull[hull.size()-1],hull[hull.size()-2]))
            hull.pop_back();
        hull.push_back(i);
    }
    int sz = hull.size();
    h = hull.size()-1;
    hull.pop_back();
    reverse(all(p));
    for(auto i : p){
        while(hull.size() > sz && check(i,hull[hull.size()-1],hull[hull.size()-2]))
            hull.pop_back();
        hull.push_back(i);
    }
    hull.pop_back();
}

void solve(){
    int ans = 0,d = h,sz = hull.size();
    rep(i,0,sz-1){
        while(check2(hull[i],hull[(i+1)%sz],hull[d],hull[(d+1)%sz]))
            d = (d+1)%sz;
        if(ans < (hull[i]-hull[d]).dis()){
            ans = (hull[i]-hull[d]).dis();
            int a = hull[i].ind,b = hull[d].ind;if(a > b)swap(a,b);
            pt_ans = {a,b};
        }
        else if(ans == (hull[i]-hull[d]).dis()){
            int a = hull[i].ind,b = hull[d].ind;if(a > b)swap(a,b);
            if(pt_ans > (pt){a,b})pt_ans = {a,b};
        }
        if(ans < (hull[(i+1)%sz]-hull[d]).dis()){
            ans = (hull[(i+1)%sz]-hull[d]).dis();
            int a = hull[(i+1)%sz].ind,b = hull[d].ind;if(a > b)swap(a,b);
            pt_ans = {a,b};
        }
        else if(ans == (hull[(i+1)%sz]-hull[d]).dis()){
            int a = hull[(i+1)%sz].ind,b = hull[d].ind;if(a > b)swap(a,b);
            if(pt_ans > (pt){a,b})pt_ans = {a,b};
        }
    }
}

signed main(){
    Orz;
    while(cin>>n){
        if(n == 0)break;
        pt_ans = (pt){0,0};
        p.resize(n,{0,0});
        rep(i,0,n-1){
            cin>>p[i].x>>p[i].y;
            p[i].ind = i;
        }
        convex_hull();
        solve();
        cout<<pt_ans.x<<" "<<pt_ans.y<<endl;
    }
}

/*
5
9 1
1 5
1 2
9 9
5 1
 */
```

### ZJ b288: 夏季大三角

[題目連結](https://zerojudge.tw/ShowProblem?problemid=b288)
[解題報告](https://zerojudge.tw/ShowThread?postid=26741&reply=0)

> 題目敘述
請輸出在N個二維平面的座標，挑選3顆出來成組成三角形的最大面積

比較一下兩個複雜度的作法，第一個是使用 $O(n^3)$ 枚舉所有的點並計算面積，所需要的時間是0.4sec，而且需要特別注意不能使用到海龍公式計算面積，否則有很大的機會會超時。

![](https://i.imgur.com/oN26CR4.png)

以下作法是先進行 $O(n\log n)$ 找尋凸包，因為面積最大的三角形必定三個點都在凸包上，因此用 $n^2$ 的時間進行枚舉，旋轉卡尺（類似最遠點對的作法）找面積最大的第三個點，就能在總時間複雜度 $O(n^2)$ 完成！（會再更少，因為只要枚舉凸包上的點）

![](https://i.imgur.com/SUwVRWv.png)

以下是AC Code：

```cpp=
#include <bits/stdc++.h>
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define rep(i,a,b) for(int i=a;i<=b;i++)
#define pii pair<int,int>
#define pdd pair<double,double>
#define int long long
#define ll long long
#define ld long double
#define N 100001
#define eps 1e-9
#define x first
#define y second

using namespace std;

struct pt{
    ld x,y;
    bool operator < (pt b){
        if(x == b.x)return y < b.y;
        return x < b.x;
    }
    bool operator > (pt b){
        if(x == b.x)return y > b.y;
        return x > b.x;
    }
    bool operator == (pt b){
        if(abs(x-b.x)<=eps && abs(y-b.y)<=eps)return true;
        return false;
    }
    pt operator+(pt b) {return {x + b.x, y + b.y};} //向量相加
    pt operator-(pt b) {return {x - b.x, y - b.y};} //向量相減
    ld operator^(pt b) {return x * b.y - y * b.x;} //向量外積cross
    ld operator*(pt b) {return x * b.x + y * b.y;} //向量內積dot
};
bool cmp(pt a, pt b){
    if(a.x == b.x)return a.y < b.y;
    return a.x < b.x;
}
vector<pt> p,hull;
int n,t,h;
ld ans;

bool check(pt a,pt b,pt o){
    int cross = (a - o)^(b - o);
    return cross >= 0;                              //這裡很關鍵，別吃WA
}

bool check2(pt a,pt b,pt c,pt d){
    ld aa = (a - c)^(b - c);
    ld bb = (a - d)^(b - d);
    return aa < bb;
}

ld area(pt a,pt b){
    return abs(a^b)/2;
}

void convex_hull(){
    hull.clear();
    sort(p.begin(),p.end(),cmp);       //首先對x進行排序
    for(auto i : p){                   //依序走訪，如果遇到外積<0則不在凸包上
        while(hull.size()>=2 && check(i,hull[hull.size()-1],hull[hull.size()-2])){
            hull.pop_back();
        }
        hull.push_back(i);             //在凸包hull的每一點都符合外積小於0
    }
    int down_hull = hull.size();
    h = down_hull-1;
    hull.pop_back();                   //x最大的點會在凸包上，不用做兩次先pop一次
    reverse(p.begin(),p.end());        //將所有點逆序之後做一次上面的凸包
    for(auto i: p){
        while(hull.size() > down_hull && check(i,hull[hull.size()-1],hull[hull.size()-2])){
            hull.pop_back();
        }
        hull.push_back(i);
    }
    hull.pop_back();
}

void solve(){
    int d,sz = hull.size();
    rep(i,0,sz-1){
        rep(j,i+1,sz-1){
            d = (j+1)%sz;
            while(check2(hull[i],hull[(j)%sz],hull[d],hull[(d+1)%sz]))
                d = (d+1)%sz;
            ans = max(ans,area((hull[d]-hull[i]),(hull[d]-hull[j])));
        }
    }
}

signed main(){
    Orz;
    cin>>n;
    p.assign(n,{0,0});
    rep(i,0,n-1)cin>>p[i].x>>p[i].y;
    convex_hull();
    ans = 0;
    solve();
    cout<<fixed<<setprecision(6);
    cout<<ans<<endl;
}
```

### TIOJ 1500 Clean up on aisle 3

[題目連結](https://tioj.ck.tp.edu.tw/problems/1500)
[Submission](https://tioj.ck.tp.edu.tw/submissions/262966)
> 題目敘述
平面上n個點找最近點對的距離

最近點對真的有超多種作法的，枚舉、掃描線、分治、隨機都可以做！這邊有[一篇筆記](/hVplrqxCRdGiMkn2lwXGAA)比較各種時間複雜度的最近點對作法，這邊不多做贅述！

以下程式碼是掃描線演算法，最差情況下的時間複雜度是 $O(n^2)$，因為需要排序，所以下限為 $\Omega(n\log n)$！

```cpp=
#include <bits/stdc++.h>
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define rep(i,a,b) for(int i=a;i<=b;i++)
#define pii pair<int,int>
#define pdd pair<double,double>
#define int long long
#define ll long long
#define ld long double
#define N 50005
#define all(x) x.begin(),x.end()
#define INF 5e18
#define eps 1e-9
#define x first
#define y second
using namespace std;
int n;
pii p[N];

ld dis(pii a, pii b){
    ld x = a.x-b.x, y = a.y-b.y;
    return sqrt(x*x + y*y);
}

signed main(){
    Orz;
    cout<<fixed<<setprecision(6);
    
    while(cin>>n){
        rep(i,0,n-1)cin>>p[i].x>>p[i].y;
        sort(p,p+n);
        ld d = INF;
        rep(i,0,n-1){
            rep(j,i+1,n-1){
                if(p[j].x > p[i].x + d)break;
                d = min(d, dis(p[i],p[j]));
            }
        }
        cout<<d<<endl;
    }
}
```

### TIOJ 1280 領土 (Territory)

[題目連結](https://tioj.ck.tp.edu.tw/problems/1280)
[Submission](https://tioj.ck.tp.edu.tw/submissions/262848)

> 題目敘述
一個國家有 n 個安全哨，每一個都有座標 $(x,y)$ ，代表在座標軸上的位置。輸出該國安全哨所能圍出的最大領土。

n個點所能圍成的最大面積，其實等價於凸包的面積。與前幾題的**最小凸多邊形**是一模一樣的題目！

```cpp=
#include <bits/stdc++.h>
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define rep(i,a,b) for(int i=a;i<=b;i++)
#define pii pair<int,int>
#define pdd pair<double,double>
#define int long long
#define ll long long
#define ld long double
#define N 100001
#define all(x) x.begin(),x.end()
#define eps 1e-9
#define x first
#define y second

using namespace std;

struct pt{
    int x,y;
    bool operator < (pt b){
        if(x == b.x)return y < b.y;
        return x < b.x;
    }
    bool operator > (pt b){
        if(x == b.x)return y > b.y;
        return x > b.x;
    }
    bool operator == (pt b){
        if(x-b.x == 0 && y-b.y == 0)return true;
        return false;
    }
    pt operator+(pt b) {return {x + b.x, y + b.y};} //向量相加
    pt operator-(pt b) {return {x - b.x, y - b.y};} //向量相減
    int operator^(pt b) {return x * b.y - y * b.x;} //向量外積cross
    int operator*(pt b) {return x * b.x + y * b.y;} //向量內積dot
};

vector<pt> p,temp,pp;
vector<int>  cnt;
int n,ans = 0;

bool cmp(pt a, pt b){
    if(a.x == b.x)return a.y < b.y;
    return a.x < b.x;
}

bool check(pt a,pt b,pt o){
    pt aa = a - o;
    pt bb = b - o;
    return (aa^bb) >= 0;
}

vector<pt> solve(){
    sort(all(p),cmp);
    vector<pt> h;
    for(pt i : p){
        while(h.size()>=2 && check(i,h[h.size()-1],h[h.size()-2]))
            h.pop_back();
        h.push_back(i);
    }
    int sz = h.size();
    h.pop_back();
    reverse(all(p));
    for(auto i : p){
        while(h.size()>sz && check(i,h[h.size()-1],h[h.size()-2]))
            h.pop_back();
        h.push_back(i);
    }
    return h;
}

signed main(){
    Orz;
    cin>>n;
    p.resize(n,{0,0});
    rep(i,0,n-1)cin>>p[i].x>>p[i].y;
    vector<pt> hull = solve();
    int area = 0,sz = hull.size();
    rep(i,0,sz-2){
        area += (hull[i]^hull[i+1]);
    }
    cout<<((area%2)?(area/2)+1:(area/2))<<endl;
}
```

### ZJ a871: Museum Area

[題目連結](https://zerojudge.tw/ShowProblem?problemid=a871)

> 題目敘述
n個點圍成的多邊形，求面積

水題，直接套行列式公式即可算出答案！

```cpp=
#include <bits/stdc++.h>
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define rep(i,a,b) for(int i=a;i<=b;i++)
#define pdd pair<double,double>
#define int long long
#define ld long double
#define N 15
#define x first
#define y second
using namespace std;

int n;
pdd p[N];

ld check(pdd a, pdd b){
    return a.x*b.y - a.y*b.x;
}

signed main(){
    Orz;
    while(cin>>n){
        rep(i,0,n-1)cin>>p[i].x>>p[i].y;
        p[n] = p[0];
        ld area = 0.0;
        rep(i,0,n-1)area += check(p[i],p[i+1]);
        ld ans = (ld)area/2;
        if(ans<0)ans = -ans;
        cout<<fixed<<setprecision(2);
        cout<<ans<<endl;
    }
}
```

### TIOJ 1678 剪多邊形（molding）

[題目連結TIOJ](https://tioj.ck.tp.edu.tw/problems/1678)
[TIOJ Submission](https://tioj.ck.tp.edu.tw/submissions/262849)
[題目連結ZJ](https://zerojudge.tw/ShowProblem?problemid=d546)

> 題目敘述
間單來說是求出多邊形面積以及凸包面積的差，詳細可以點上面題目連結。

題目說多邊形需要才剪下的面積，我們就算凸包面積以及多邊形面積，兩者的差去除上題目給的色塊面積即是答案！

```cpp=
#include <bits/stdc++.h>
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define rep(i,a,b) for(int i=a;i<=b;i++)
#define pii pair<int,int>
#define pdd pair<double,double>
#define int long long
#define ll long long
#define ld long double
#define N 100001
#define all(x) x.begin(),x.end()
#define eps 1e-9
#define x first
#define y second

using namespace std;

struct pt{
    int x,y;
    bool operator < (pt b){
        if(x == b.x)return y < b.y;
        return x < b.x;
    }
    bool operator > (pt b){
        if(x == b.x)return y > b.y;
        return x > b.x;
    }
    bool operator == (pt b){
        if(x-b.x == 0 && y-b.y == 0)return true;
        return false;
    }
    pt operator+(pt b) {return {x + b.x, y + b.y};} //向量相加
    pt operator-(pt b) {return {x - b.x, y - b.y};} //向量相減
    int operator^(pt b) {return x * b.y - y * b.x;} //向量外積cross
    int operator*(pt b) {return x * b.x + y * b.y;} //向量內積dot
};

vector<pt> p,temp,pp;
vector<int>  cnt;
int n,a,ans = 0;

bool cmp(pt a, pt b){
    if(a.x == b.x)return a.y < b.y;
    return a.x < b.x;
}

bool check(pt a,pt b,pt o){
    pt aa = a - o;
    pt bb = b - o;
    return (aa^bb) >= 0;
}

vector<pt> solve(){
    sort(all(p),cmp);
    vector<pt> h;
    for(pt i : p){
        while(h.size()>=2 && check(i,h[h.size()-1],h[h.size()-2]))
            h.pop_back();
        h.push_back(i);
    }
    int sz = h.size();
    h.pop_back();
    reverse(all(p));
    for(auto i : p){
        while(h.size()>sz && check(i,h[h.size()-1],h[h.size()-2]))
            h.pop_back();
        h.push_back(i);
    }
    return h;
}

int Polygon(){
    vector<pt> temp(p);
    temp.push_back(temp[0]);
    int area = 0;
    rep(i,0,n-1)area += (temp[i]^temp[i+1]);
    return abs(area);
}

signed main(){
    Orz;
    cin>>n>>a;
    p.resize(n,{0,0});
    rep(i,0,n-1)cin>>p[i].x>>p[i].y;
    ld small = (ld)Polygon()/2;
    vector<pt> hull = solve();
    int area = 0,sz = hull.size();
    rep(i,0,sz-2)area += (hull[i]^hull[i+1]);
    ld big = (ld)area/2;
    int ans = ceil((big-small)/a);
    cout<<ans<<endl;
}
```

### ZJ d269: 11579 - Triangle Trouble

[題目連結](https://zerojudge.tw/ShowProblem?problemid=d269)

> 題目敘述
有一個三角形工廠有一個很大的問題。給你一些邊的邊長，想辦法找出用這些邊長圍出最大的三角形。

根據海龍公式，三角形面積：
$$\triangle ABC = \sqrt{s\cdot(s-a)\cdot(s-b)\cdot(s-c)}$$

可以利用貪婪法，將所有邊長由大到小進行排序，每一次拿最大的三個邊長進行枚舉，即可算出最大的三角形面積。不難理解，當換上一個比較大的邊，算出來的s也會比較大，跟邊相減的值也會比較大，總面積自然較大（好啦，這是非常不嚴謹的證明XD）

在想題過程中，我有思考到，如果周長一樣的情況下，到底何種面積的三角形面積會比較大？答案是正三角形！

{% note default %}
**三角形周長固定下面積的比較**
根據海龍公式：
$$s = \frac{1}{2}(a+b+c)$$

想要比較在周長固定下三角形的面積，可以用算幾不等式比較，因為 $s$ 是定值，所以可以列出以下式子：
$$\frac{(s-a)+(s-b)+(s-c)}{3} ≥ \sqrt[3]{(s-a)(s-b)(s-c)}$$
等好成立時，$a=b=c$。因為$s = \frac{a+b+c}{2}$，因此：
$$(\frac{a}{2})^2 ≥ (s-a)(s-b)(s-c)$$

得到海龍公式
$$\triangle ABC = \sqrt{s\cdot(s-a)\cdot(s-b)\cdot(s-c)} ≤ \sqrt{\frac{3a}{2}\cdot\frac{a^3}{8}}=\frac{\sqrt{3}}{4}a^2$$
{% endnote %}

以下是使用貪婪法的AC Code：

```cpp=
#include <bits/stdc++.h>
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define rep(i,a,b) for(int i=a;i<=b;i++)
#define pii pair<int,int>
#define pdd pair<double,double>
#define ll long long
#define ld double
#define N 100001
#define all(x) x.begin(),x.end()
#define eps 1e-9
#define x first
#define y second
using namespace std;
int t,n;
vector<ld> p;

ld area(ld a ,ld b, ld c){
    if(a > b + c)return -1;
    ld p = (a+b+c)/2;
    return p*(p-a)*(p-b)*(p-c);
}

signed main(){
    Orz;
    cin>>t;
    while(t--){
        cin>>n;
        p.assign(n,0);
        rep(i,0,n-1)cin>>p[i];
        sort(all(p),greater<>());
        ld ans = 0;
        rep(i,0,n-3)
            ans = max(ans,area(p[i],p[i+1],p[i+2]));
        cout<<fixed<<setprecision(2);
        cout<<sqrt(ans)<<endl;
    }
}
```

## 心得

計算幾何，顧名思義就是在電腦完成幾何的運算，要怎麼把平面的東西轉化成電腦看得懂的東西就是計算幾何在做的事情。常常我們覺得很容易判斷的事情，例如判斷線段是否相交，我們可以利用肉眼直輕易判斷出來，因為我們有強大的空間感幫助我們進行判斷，但換作是電腦就必須用一些數學的技巧，對於不同的情況做各自的判斷，才能讓電腦正確回答兩條線段的相交情形。

除此之外，在寫題過程中，使用到ggb進行輔助，讓我可以對程式的執行過程有更是覺化的概念，也幫助我在解題時能更理解解題的策略！上面一題三角形個數的判斷，就使用了ggb判定將點搬移的所有情況。利用它我抓到了當點的y座標為零時並沒進行好特殊情況的判斷，這也是一個視覺化之後的好處！

有一題沒有做的是模擬退火的題目實作，要求圓與三角形的交集面積，感覺超級複雜，以後有時間來慢慢實作！
