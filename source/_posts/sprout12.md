---
title: 資芽第十二週：隨機算法
date: 2021-5-29
tags: 
    - 資芽筆記
    - 隨機
categories: 資訊之芽筆記
mathjax: true
#password: sprout2022
---

## 上課內容

這一週講隨機，主要講rolling hash，字串的hash、隨機的例題，像是矩陣乘法的驗證$O(n^2)$、訪問區間各元素出現次數是否為k的倍數，最近點對的$O(n)$作法等（還有東西還沒有看XD）

<!--more-->

## 上機作業

### 欸迪的字串

[題目連結](https://neoj.sprout.tw/problem/265/)
輸入有兩行，第一行包含一個長度介於[1,500000]的字串S，第二行包含一個長度介於[1,500000]的字串T，請輸出一串遞增的數列表示字串S出現在字串T的哪些地方。

**Rolling hash**：
$$H(s[1:n]) = \sum_{i=0}^n S_i\times C^{n-i}$$
我們如果假設我們要找的目標字串S，長度為m，他的雜湊值是：
$$H(S)= \sum_{i=1}^mS_i \times C^{m-i}$$
則對於長度為L與r-1的字串T，其hash值為：
$$H(T[1:L]) = \sum_{i=1}^LT_i\times C^{L-i}$$
$$H(T[1:r-1]) = \sum_{i=1}^{r-1}T_i\times C^{r-1-i}$$
則目標區間長度為m的字串長度即為字串S的雜湊值，將T[1:L]的雜湊值扣掉$C^{m}$乘上T[1:r-1]的雜湊值，即為所求。

$$\begin{split}H(S)&=H(T[r:L])\\ &=H(T[1:L])-H(T[1:r-1])\\
&= \sum_{i=1}^LT_i\times C^{L-i} - C^{m}\times \sum_{i=1}^{r-1}T_i\times C^{r-1-i} \\
&=\sum_{i=1}^LT_i\times C^{L-i}-\sum_{i=1}^{r-1}T_i\times C^{L-i} \\
&= \sum_{i=r}^{L}T_i\times C^{L-i} \\
&= \sum_{i=1}^{m}T_{i+r-1}\times C^{m-1}\end{split}$$
就可以得到當前區間的hash值
所以這整題就變成維護字串T的前綴和，透過以上方式O(n)得到每一個index的hash值，接著O(1)比對即可。
{% note success %}
Q:照最基本的暴力比對可不可行？
A:可以構造出需要比對很多次的字串，有可能會被卡TLE之類的![](https://i.imgur.com/02iE9me.png)
{% endnote %}
以後一定要記得，必須要行末**輸出一行**，我在這邊WA超久

```cpp=
#include <bits/stdc++.h>
#define int unsigned long long int
#define INF 0x3f3f3f3f
#define N 500005
#define mod 1000000007
using namespace std;
int power[N],hash_func[N],charc[30],n,m,C = 137;
char target[N],a[N];
vector<int> vec;

void init(){
    power[0] = 1;
    srand(time(NULL));
    for(int i=1;i<=m;i++){
        power[i] = ((power[i-1]*C)+mod)%mod;
    }
    for(int i=0;i<30;i++){    //再用rand來使字串更亂（不用也沒差）
        int temp = rand();
        charc[i] = temp;
    }
}
signed main(){
    scanf("%s\n%s",target+1,a+1);
    m = strlen(target+1);
    n = strlen(a+1);
    init();
    hash_func[0] = 0;
    
    for(int i=1;i<=n;i++){
        hash_func[i] = ((C*hash_func[i-1]+(charc[a[i]-'a']))+mod)%mod;
    }
    int sum = 0;
    for(int i=1;i<=m;i++){
        sum = ((sum*C+(charc[target[i]-'a']))+mod)%mod;
    }
    for(int i=0;i<=n-m;i++){
        if(sum == ((hash_func[i+m]-((hash_func[i]*power[m])+mod)%mod)+mod)%mod)
            vec.push_back(i);
    }
    int len = vec.size();
    
    if(len>0)printf("%llu",vec[0]);
    for(int i=1;i<len;i++){
        printf(" %llu",vec[i]);
    }
    cout<<endl;    //這一行是毒瘤
}
```

### 溫力的故事

[題目連結](https://neoj.sprout.tw/problem/266/)
給字串集合A，接下來給m個字串，問你在m個字串中i字串一共出現了幾次。
直覺的想法就是直接用multiset來解這一題。

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define int unsigned long long int
#define N 105
using namespace std;
int n,m,power[N],C = 13331;

void init(){
    power[0] = 1;
    for(int i=1;i<=N;i++){
        power[i] = power[i-1]*C;
    }
}

int func(string s){
    int len = s.size(),sum = 0;
    for(int i=1;i<=len;i++){
        sum+=((int)s[i-1]*power[len-i]);
    }
    return sum;
}

signed main(){
    ios;
    init();
    multiset<int> ss;
    cin>>n>>m;
    for(int i=0;i<n;i++){
        string s;cin>>s;
        int temp = func(s);
        ss.insert(temp);
    }
    for(int i=0;i<m;i++){
        string s;cin>>s;
        int temp = func(s);
        cout<<ss.count(temp)<<endl;
    }
}
```

### 想不到題目標題QQ

[題目連結](https://neoj.sprout.tw/problem/793/)
圖中的-2改成-(k-1)，對於圖中的x,y都是設計成大數亂數，要承擔的風險就是這些大數相加之後不應該變成0的卻變成0（機率超小）
還有一個重點，對於每一筆詢問都要求出區間的和，因此要用O(n)維護前綴和！（一開始沒想到tle，不過靈光一閃維護前綴和就過了）
![](https://i.imgur.com/Z4NzA4J.png)

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define int long long int
#define N 500005
#define mod 1000000007
using namespace std;
int n,k,m,arr[N],times[N],fuck[N],value[N],pre[N];

void init(){
    memset(times, 0, sizeof(times));
    srand(time(NULL));
    pre[0] = 0;
    for(int i=0;i<=N;i++){
        int temp = rand()%mod;
        fuck[i] = temp;
    }
}

signed main(){
    ios;
    init();
    cin>>n>>k>>m;
    for(int i=1;i<=n;i++){
        cin>>arr[i];
        times[arr[i]]++;
        if(times[arr[i]]%k==0){
            value[i] = -(k-1)*fuck[arr[i]];
        }
        else{
            value[i] = fuck[arr[i]];
        }
        pre[i] = (pre[i-1]+value[i]);
    }

    for(int i=1;i<=m;i++){
        int l,r,sum=0;cin>>l>>r;
        sum = pre[r]-pre[l-1];
        if(sum==0)cout<<1;
        else cout<<0;
    }
    cout<<endl;
}
```

### 矩陣乘法

[題目連結](https://neoj.sprout.tw/problem/740/)
給定三個矩陣ABC，驗證A乘上B是否為C。
當然可以用$O(n^3)$的時間實際驗證A乘上B的結果與C比對，但其實有更快的方法
如下，構造出矩陣R，這一題可以思考要1乘上多少的矩陣，透過兩次O(n^2)的乘法，就可以比對這個R矩陣乘上C矩陣的結果
這題蠻簡單的，一次就過了
![](https://i.imgur.com/LvdbXi8.png)

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define int long long int
#define N 1505
#define mod 1000000007
using namespace std;
int n,m,k,a[N][N],b[N][N],c[N][N],R[N],temp[N],c_new[N];

void init(){
    memset(temp, 0, sizeof(temp));
    srand(time(NULL));
    for(int i=0;i<N;i++){
        int temp = rand()%mod;
        R[i] = temp;
    }
}

void input(){
    cin>>n>>m>>k;
    for(int i=0;i<n;i++){
        for(int j=0;j<m;j++){
            cin>>a[i][j];
        }
    }
    for(int i=0;i<m;i++){
        for(int j=0;j<k;j++){
            cin>>b[i][j];
        }
    }
    for(int i=0;i<n;i++){
        for(int j=0;j<k;j++){
            cin>>c[i][j];
        }
    }
}

signed main(){
    ios;
    init();
    input();
    for(int i=0;i<m;i++){
        for(int j=0;j<k;j++){
            temp[i]+=(b[i][j]*R[j])%mod;
        }
    }
    for(int i=0;i<n;i++){
        for(int j=0;j<m;j++){
            c_new[i]+=(a[i][j]*temp[j])%mod;
        }
    }
    bool flag = 1;
    for(int i=0;i<n;i++){
        int sum = 0;
        for(int j=0;j<k;j++){
            sum+=(c[i][j]*R[j])%mod;
        }
        if(c_new[i]!=sum){
            flag = 0;
            break;
        }
    }
    if(flag)cout<<"Yes"<<endl;
    else cout<<"No"<<endl;
}
```

### 最近點對

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

## 手寫作業

不用交手寫作業！不過還是有一份「欣賞用」的，講Disjoint Set
