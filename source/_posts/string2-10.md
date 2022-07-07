---
title: "[題解]TIOJ 1497 喝醉的宿主 The drunk host"
date: 2021-1-9
tags: 
    - 字串
    - 題解
categories: 字串題解
mathjax: true
---

### TIOJ 1497 喝醉的宿主 The drunk host
<!-- more -->
[題目連結](https://tioj.ck.tp.edu.tw/problems/1497)
[Submission](https://tioj.ck.tp.edu.tw/submissions/264189)
> 題目敘述
裸後綴數組。

後綴數組我看了好久（大概有4天吧），一直對著它發呆，不知道它的精髓到底在哪裡。。剛好又遇到開學，不能整天快樂寫題XD

#### 倍增優化

對著螢幕發呆的日子終於結束了，直到我看了[這一篇](https://oi-wiki.org/string/sa/)（雖然說我前幾天也有看但看不懂，可能是消化的天數不夠多吧），尤其是裡面的一張圖，深刻說明了倍增的精髓。

後綴數組求法就不多解釋（上面有），放幾個實作上的小細節。

{% note primary %}
**實作小細節**
我們要求進行 $O(\log n)$ 層的倍增，可以使用C++內建的log10()再用ceil去處理，但顯然有點慢，如果套用以下函數，它會回傳數字二進位之後最大的1前面總共有多少個0（前綴0的數量），與32相減（32是long long的關係）就是我們要的log的次數。

```cpp=
int lg = __builtin_clz(n)
```

另外一個小小細節，就是在字串的後半部分，如果跟前面一樣倍增一個比較大的數字，有可能會超出範圍，這時候初始值就很重要了！初始設定rk[n] = -1，導致任何只要超出範圍的都會取到這個-1，表示只要後面沒有字串、如果前面都相同但長度比較長的字串比起來，較短的會排在比較前面的位置！
{% endnote %}
這個算法是 $O(n\log^2 n)$，TIOJ這一題可以過，不過到[SPOJ](https://www.spoj.com/problems/SARRAY/)就會被卡TLE

> $O(n^2 log(n))$ is expected to score about 20-30. (Naive sorting all suffixes)
> $O(n log^2(n))$ is expected to score about 40. (OK for most programming contest problems)
> $O(n log n)$ is expected to score about 60-70. (Use counting sort for small alphabet size)
> $O(n)$ without tweaks is expected to score about 80-90.
> $O(n)$ with tweaks is expected to score 100. (This is meant for fun only :)

越後面就越毒瘤XD

> test 1 - AC (score=0.000000, sig=0, time=0.009123, mem=5372)
> test 2 - AC (score=0.000000, sig=0, time=0.006427, mem=5508)
> test 3 - AC (score=0.000000, sig=0, time=0.014975, mem=5468)
> test 4 - AC (score=0.000000, sig=0, time=0.031332, mem=5536)
> test 5 - AC (score=0.000000, sig=0, time=0.026948, mem=5456)
> test 6 - AC (score=0.000000, sig=0, time=0.023113, mem=5396)
> test 7 - TLE (score=0.000000, sig=0, time=0.210000, mem=7340)
> test 8 - TLE (score=0.000000, sig=0, time=0.210000, mem=7152)
> test 9 - AC (score=0.000000, sig=0, time=0.170094, mem=7284)
> test 10 - AC (score=0.000000, sig=0, time=0.140098, mem=7340)

#### 程式碼

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld long double
#define int long long
#define N 100005
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define all(x) x.begin(),x.end()
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int n,sa[N];

struct pt{
    int x,y,id;
    bool operator==(pt b){
        if(x==b.x && y==b.y)return true;
        return false;
    }
};
//第一位、第二位比較以及後綴編號

bool cmp(pt a,pt b){
    if(a.x != b.x)return a.x < b.x;
    return a.y < b.y;
}

void suffix_array(string S){
    n = S.size();
    int lg = 32-__builtin_clz(n);
    //回傳第一個1之前0的個數（二進位）
    vector<pt> cur(n,{0,0,0});
    vector<int> rk(n+1,-1);
    rep(i,0,n-1)cur[i] = {S[i],0,i};
    rep(p,0,lg){
        int k = 1 << p;         //現在倍增的大小
        sort(all(cur),cmp);
        rk[cur[0].id] = 0;
        rep(i,1,n-1){
            rk[cur[i].id] = (cur[i-1]==cur[i] ? rk[cur[i-1].id]:i);
            //設定rk，與前一個相同則設定跟前一個一樣
        }
        rep(i,0,n-1){
            cur[i] = {rk[i],rk[min(n,i+k)],i};
        }
    }
    sort(all(cur),cmp);
    rep(i,0,n-1)sa[i] = cur[i].id;
}

signed main(){
    Orz;
    string S;getline(cin,S);
    suffix_array(S);
    rep(i,0,n-1)cout<<sa[i]<<'\n';
}
```

#### Radix Sort 優化

[Submission](https://tioj.ck.tp.edu.tw/submissions/264407)

比較一下 $O(n\log^2 n)$ 以及 $O(n\log n)$ 的時間，兩者花了近兩倍的時間差距。看了一下這一題的TopCoder，竟然可以做到十位數毫秒！如果要繼續優化成線性 $O(n)$ 的複雜度，就會使用到 DC3 的演算法，雖然好像很複雜不實用QQ

![](https://i.imgur.com/tQph2uA.png)

![](https://i.imgur.com/1wcKazZ.png)

![](https://i.imgur.com/i4WSvw8.png)

使用 $O(n\log^2n)$ 的算法會TLE第八、九筆測資，不過使用基數排序就可以AC了！

> test 1 - AC (score=0.000000, sig=0, time=0.006565, mem=6048)
> test 2 - AC (score=0.000000, sig=0, time=0.006594, mem=6300)
> test 3 - AC (score=0.000000, sig=0, time=0.012431, mem=7860)
> test 4 - AC (score=0.000000, sig=0, time=0.012267, mem=7208)
> test 5 - AC (score=0.000000, sig=0, time=0.011158, mem=6804)
> test 6 - AC (score=0.000000, sig=0, time=0.012057, mem=7892)
> test 7 - AC (score=0.000000, sig=0, time=0.140876, mem=17720)
> test 8 - AC (score=0.000000, sig=0, time=0.077631, mem=21952)
> test 9 - AC (score=0.000000, sig=0, time=0.074905, mem=21340)
> test 10 - AC (score=0.000000, sig=0, time=0.076732, mem=30160)

#### 程式碼

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld long double
#define N 100005
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define all(x) x.begin(),x.end()
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int n,sa[N];

struct pt{
    int x,y,id;
    bool operator==(pt b){
        if(x==b.x && y==b.y)return true;
        return false;
    }
};
//第一位、第二位比較以及後綴編號
vector<pt> temp,box[N];

void init(){
    temp.clear();
    rep(i,0,n-1)box[i].clear();
}

bool cmp(pt a,pt b){
    if(a.x == b.x)return a.y < b.y;
    return a.x < b.x;
}

void raddix_sort(vector<pt> &cur){
    init();
    rep(i,0,n-1){
        if(cur[i].y == -1)temp.push_back(cur[i]);
        else box[cur[i].y].push_back(cur[i]);
    }
    rep(i,0,n-1)
        for(auto j : box[i])temp.push_back(j);
    rep(i,0,n-1)box[i].clear();
    rep(i,0,n-1)
        box[temp[i].x].push_back(temp[i]);
    cur.clear();
    rep(i,0,n-1)
        for(auto j : box[i])cur.push_back(j);
}

void suffix_array(string S){
    n = S.size();
    int lg = 32-__builtin_clz(n);//回傳第一個1之前0的個數（二進位）
    vector<pt> cur(n,{0,0,0});
    vector<int> rk(n+1,-1);
    rep(i,0,n-1) cur[i] = {S[i],0,i};
    sort(all(cur),cmp);
    rk[cur[0].id] = 0;
    rep(i,1,n-1)
        rk[cur[i].id] = (cur[i]==cur[i-1])?rk[cur[i-1].id]:i;
    rep(i,0,n-1)cur[i] = {rk[i],0,i};
    rep(p,0,lg){                //進行O(lgn)次
        int k = 1 << p;         //現在倍增的大小
        raddix_sort(cur);
        rk[cur[0].id] = 0;
        rep(i,1,n-1)            //設定rk，與前一個相同則設定跟前一個一樣
            rk[cur[i].id] = (cur[i-1]==cur[i] ? rk[cur[i-1].id]:i);
        rep(i,0,n-1)            //倍增pair的second
            cur[i] = {rk[i],rk[min(n,i+k)],i};
    }
    raddix_sort(cur);
    rep(i,0,n-1)sa[i] = cur[i].id;
}

signed main(){
    Orz;
    string S;getline(cin,S);
    suffix_array(S);
    rep(i,0,n-1)cout<<sa[i]<<'\n';
}
```
