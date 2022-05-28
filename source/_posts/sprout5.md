---
title: 資芽5-8週：貪心、分治、動態規劃
date: 2021-4-3
tags: 
    - 資芽筆記
    - 貪心
categories: 資訊之芽筆記
mathjax: true
---

# 資芽第五週：貪心算法

這一週主題是**Greedy**，其實貪心法的題目要難可以到很難，有點像一個抽象的想法，需要透過反覆的實作與練習（顯然我缺乏），來更容易的想到題目的解法！

## 上課內容

影片的內容主要是在證明greedy 的正確性，超複雜的數學證明（又是數學證明！）
原本以為上課會延續證明的內容，沒想到完全沒有講到證明的內容，都是在講解例題（也不錯，有很多題需要慢慢理解）
所以列了上課講過的例題，一定要搞懂！

<!--more-->

![](https://i.imgur.com/l9YwEbU.png)
絕對不要輕忽 Greedy 啊，想法看似單純，要想出解法，還是需要經驗的！

## 上機作業

### Add all(UVA)

[題目連結](https://neoj.sprout.tw/problem/70/)
這一題可以輕鬆觀察出規律：數字越早被相加，被加總的次數就會越多
因此，維護一個priority_queue，每次pop出最小的兩個數字再push 回去，可以保證總和一定會是最小的

```cpp=
#include <bits/stdc++.h>
#define int long long
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n;

signed main(){
    ios;
    priority_queue<int, vector<int>, greater<int> >qq;
    while(cin>>n && n!=0){
        for(int i=0;i<n;i++){
            int temp;cin>>temp;
            qq.push(temp);
        }
        int ans = 0;
        while(qq.size()>1){
            int a,b;
            a = qq.top();qq.pop();
            b = qq.top();qq.pop();
            qq.push(a+b);
            ans+=(a+b);
        }
        cout<<ans<<endl;
        qq.pop();
    }
}

```

### 円円攻略黃河(TIOJ)

[題目連結](https://neoj.sprout.tw/problem/74/)
這一題要求最長的長短相間的序列長度，雖然知道是Greedy ，但第一時間想不到具體的作法
卡了有一點久，總共嘗試了三次，前兩次以為對，但WA 後還是發現其中的Bug

1. 從第一個數字開始，找到下一個比自己小的數字，再找比那一個大的，重複幾次直到結束
{% note info %}
問題在於，第一個數字可能很小，導致接下來的序列長度很短
{% endnote %}
2. 找到第一個「下一個數比自己小」的數字，接著重複第1點的步驟
{% note info %}
因為要保證序列是最長的，會希望在找比自己大或小的數字時可以盡量最大或最小，才可以確保所有數字能有機會被包含進去
{% endnote %}
3. 對每一個數字如果要找大於自己的，就一直繼續往上找直到最大，如果要找最小的也是一樣
{% note info %}
以 *5 1 2 4 5 3 6* 來說，就是從5開始，接下來是1（最小了），接下來往上找，可以找到2, 4, 5，而為了盡量讓數字大才能包到接下來的數字（如果選2下一個就不能是3），所以選5，再來就是3 跟6 。
所以序列為： *5 1 5 3 6* ， 長度為5
{% endnote %}
想說應該要AC 了，沒想到沒有memset 所以吃了一個WA ！

```cpp=
#include <bits/stdc++.h>
#define int long long
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int t;

signed main(){
    ios;
    cin>>t;
    int n,arr[1000005];
    while(t--){
        cin>>n;
        memset(arr,0,sizeof(arr));
        for(int i=0;i<n;i++)cin>>arr[i];
        int height = 0, ind = 0, ans = 1;//0->down 1->up
        while(ind<n && arr[ind]<=arr[ind+1])ind++;
        
        while(ind<n){
            if(height == 0 && arr[ind]!=arr[ind+1]){
                while(arr[ind]>=arr[ind+1])ind++;
                height = 1;
            }
            else if(height == 1 && arr[ind]!=arr[ind+1]){
                while(arr[ind]<=arr[ind+1])ind++;
                height = 0;
                ans+=2;
            }
            ind++;//如果有連續相同數字需要加
        }
        cout<<ans<<endl;
    }
}
```

### 誰先晚餐

[題目連結](https://neoj.sprout.tw/problem/89/)
按照Greegy 的想法，吃最慢的人要最先吃，吃最快的人要後吃，才能讓整個時間盡量的縮短
這一題之前學長講解過，所以自然是比較簡單。But! 要怎麼證明這樣可以得到最佳解呢～留到手寫作業！

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n;

struct student{
    int eat,cook;
}people[10005];

bool comp(student a,student b){
    return a.eat > b.eat;
}
int main(){
    ios;
    while(cin>>n){
        if(n==0)break;
        for(int i=0;i<n;i++){
            cin>>people[i].cook>>people[i].eat;
        }
        sort(people, people+n, comp);
        int ans = 0,cook = 0;
        for(int i=0;i<n;i++){
            cook+=people[i].cook;//煮飯時間一定總數不會改變
            int leave = cook+people[i].eat;
            ans = max(ans, leave);
        }
        cout<<ans<<endl;
    }
}
```

### 包裝禮物

[題目連結](https://neoj.sprout.tw/problem/78/)
一直吃WA ，吃到懷疑人生哎～
想法：由大方塊到小方塊，盡量填滿，如果過程中大小為4的方塊變成負的，可以先欠著，直到最後一次由大小為1的來補，這樣可以確保每一格方塊不是被1*1的填滿就是剩下最少的空格

```cpp=
#include <bits/stdc++.h>
#define int long long
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int a,b,c,d,e,f;

signed main(){
    ios;
    while(cin>>a>>b>>c>>d>>e>>f){
        if(a==0&&b==0&&c==0&&d==0&&e==0&&f==0)break;
        int ans = f+e+d;
        a = a - 11*e;//5*5可以用11個1*1填
        b = b - 5*d;//一個4*4可以用5個2*2填
        ans+=(c/4);
        if(c%4!=0)ans++;
        if(c%4==1){
            a = a - 7;
            b = b - 5;
        }
        else if(c%4==2){
            a = a - 6;
            b = b - 3;
        }
        else if(c%4==3){
            a = a - 5;
            b = b - 1;
        }
        if(b>0){
            ans+=(b/9);
            if(b%9!=0)ans++;
            a = a-(36-4*(b%9));
        }
        else if(b<0)a = a-(4*(-b));//如果b有欠，可以用a來補
        if(a>0){
            ans+=a/36;
            if(a%36!=0)ans++;
        }
        cout<<ans<<endl;
    }
}
```

### 調校高棕櫚

[題目連結](https://neoj.sprout.tw/problem/91/)
如果想要讓數字盡量小，那勢必要讓數字的位數盡量少
因此可以先把3個2合併成8，2個3合併成9
而剩下的2跟3可以有6 種組合：

| 數字個數 |0個2 | 1個2 |2個2|
| - | - | - |-|
|0個3|無  | 2 |4|
|1個3|2  | 6 |2,6|

先把數字做質因數分解，如果有質數大於10，就輸出-1
接下來，再用這6種組合用**if** 排列組合就完成了

```cpp=
#include <bits/stdc++.h>
#define int long long
using namespace std;
int t;

signed main(){
    cin>>t;
    while(t--){
        int n,arr[11]={0};cin>>n;
        if(n==1){
            cout<<1<<endl;
            continue;
        }
        int ind = 2;
        while(ind<=9){
            if(n%ind==0){
                n /= ind;
                arr[ind]++;
            }
            else ind++;
        }
        if(n>10){
            cout<<-1<<endl;
            continue;
        }
        vector<int>vec;
        
        for(int i=0;i<arr[2]/3;i++)vec.push_back(8);
        arr[2]=arr[2]%3;

        for(int i=0;i<arr[3]/2;i++)vec.push_back(9);
        arr[3]=arr[3]%2;
        
        if(arr[2]==1 && arr[3]==0)vec.push_back(2);
        if(arr[2]==2 && arr[3]==0)vec.push_back(4);
        if(arr[2]==0 && arr[3]==1)vec.push_back(3);
        if(arr[2]==1 && arr[3]==1)vec.push_back(6);
        if(arr[2]==2 && arr[3]==1){
            vec.push_back(2);
            vec.push_back(6);
        }
        for(int i=0;i<arr[5];i++)vec.push_back(5);
        for(int i=0;i<arr[7];i++)vec.push_back(7);
        sort(vec.begin(),vec.end());
        for(auto i:vec)cout<<i;
        cout<<endl;
    }
}
```

## 手寫作業

這一週手寫作業是greedy 正確性的證明，把覺得應該**怎麼**做的想法轉換成數學式子表達，才能讓我們安心的使用greedy ！
蠻開心的，上一次以為分數會很低的手寫作業竟然拿到了100/110，就差一點就可以拿滿分了QQ

# 資芽第六週：分治算法

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

# 資芽第七週：動態規劃（一）

## 上課內容

- **使用DP時機：**
  - 最優解問題
  - 計數問題
  - 重複子問題

- **DP 使用過程**
  - 定義狀態
  - 狀態轉移
  - 找到一個會AC的作法，再慢慢優化

- **使用注意事項：**
  - 寫好時間複雜度
  - 用「拉」的
  - 用「推」的
  - 盡量使用由下而上

- **DP例題**
  - 跑步問題（zj b589）
  - 最大連續和
  - 最大不連續和（npsc 2017）
  - Chest of Drawers (UVA 11420)
  - 最大和矩陣問題
  - 矩陣最大方形
  - 矩陣乘法問題
  - 消消樂（UVA 10559 Blocks）

## 上機作業

### 円円數磁磚

[題目連結](https://neoj.sprout.tw/problem/138/)
我不會寫轉移式子啊...
首先可以觀察到，因為兩個磁磚的排法有3種，首先可以列出$f(n) = 3\times f(n-2)$ 這個式子，但觀察之後可以發現，如果排列的磁磚不能被兩兩分割，也可是一種新的排法。而對於固定的尺寸大小只會有一種排法，但因為可以顛倒放，因此視為兩種組合。
**轉移式：**
$f(n) = 3\times f(n-2)+2\times(f(n-4)+f(n-6)...)$
經過數學帶入消去：
$f(n-2) = 3\times f(n-4)+2\times(f(n-6)+f(n-8)...)$
$f(n) = 4\times f(n-2)-f(n-4)$

**邊界：**$dp[0] = 1, dp[2] = 3$

```cpp=
#include <bits/stdc++.h>
#define int unsigned long long
using namespace std;
int t;

signed main(){
    cin>>t;
    int dp[100005];
    memset(dp, 0, sizeof(dp));
    dp[0] = 1;dp[2] = 3;dp[3] = 0;
    for(int i=4;i<=100001;i+=2)dp[i] =(4*dp[i-2]-dp[i-4]+1000007)%1000007;
    while(t--){
        int n;cin>>n;
        cout<<dp[n]<<endl;
    }
}
```

### 円円送禮物

[題目連結](https://neoj.sprout.tw/problem/140/)
以後寫DP幾種組合問題就是像這一題一樣暴力破解
把每一種情況都寫清楚，不惜開三維陣列也沒關係
總之，$dp[i][k][j]$ 代表長度為i，左邊為顏色k，右邊為顏色j
（紅色：0、綠色：1、藍色：2）
列出每一種狀態的轉移式就好
不過，即使頭尾是一藍一綠，但也必須轉移，因為一藍一綠可以在頭或尾加上其他顏色，組成合法解，不過不要輸出就好了

```cpp=
#include <bits/stdc++.h>
#define int long long
#define ios ios::sync_with_stdio(0),cin.tie(0);
using namespace std;
int t;

signed main(){
    ios;
    int dp[100005][3][3];//0:red,1:green,2:blue
    dp[1][0][0] = 1;dp[1][1][1] = 1;dp[1][2][2] = 1;
    dp[2][0][0] = dp[2][0][1] = dp[2][0][2] = 1;
    dp[2][1][0] = dp[2][1][1] = dp[2][2][0] = dp[2][2][2] = 1;
    dp[2][1][2] = dp[2][2][1] = 0;

    for(int i=3;i<100005;i++){
        dp[i][0][0] = (dp[i-1][0][0]+dp[i-1][0][1]+dp[i-1][0][2])%1000007;
        dp[i][0][1] = (dp[i-1][0][0]+dp[i-1][0][1])%1000007;
        dp[i][0][2] = (dp[i-1][0][0]+dp[i-1][0][2])%1000007;
        
        dp[i][1][0] = (dp[i-1][1][0]+dp[i-1][1][1]+dp[i-1][1][2])%1000007;
        dp[i][1][1] = (dp[i-1][1][0]+dp[i-1][1][1])%1000007;
        dp[i][1][2] = (dp[i-1][1][0]+dp[i-1][1][2])%1000007;
        
        dp[i][2][0] = (dp[i-1][2][0]+dp[i-1][2][1]+dp[i-1][2][2])%1000007;
        dp[i][2][1] = (dp[i-1][2][0]+dp[i-1][2][1])%1000007;
        dp[i][2][2] = (dp[i-1][2][0]+dp[i-1][2][2])%1000007;
        
    }
    int n;cin>>n;
    while(n--){
        int a;cin>>a;
        cout<<(dp[a][0][0]+dp[a][0][1]+dp[a][0][2]+dp[a][1][0]+dp[a][1][1]+dp[a][2][0]+dp[a][2][2])%1000007<<endl;
    }
}
```

### 取數字1

[題目連結](https://neoj.sprout.tw/problem/141/)
**定義：**$dp[i]$ 為取第i個數字的的最大值
**轉移式：**$dp[i] = max(dp[i-2]+dp[i-2])+arr[i]$
可以發現到，因為$arr[i]$ 都是正數，因此加越多數字就代表最後的數值越大，而相鄰的兩項不能轉移，因此就要看$dp[i-2],dp[i-2]$其中i-4不用看是因為已經包含在i-2裡面了
**邊界：**$dp[1] = arr[1],dp[2] = arr[2],dp[3] = arr[1]+arr[3]$

```cpp=
#include <bits/stdc++.h>
using namespace std;
int t;

int main(){
    cin>>t;
    while(t--){
        int n,arr[100005];cin>>n;
        int dp[10005];
        memset(dp,0,sizeof(dp));
        memset(arr,0,sizeof(arr));

        for(int i=1;i<=n;i++)cin>>arr[i];
        dp[1] = arr[1];dp[2] = arr[2];dp[3] = arr[1]+arr[3];
        for(int i=4;i<=n+1;i++){
            dp[i] = max(dp[i-2],dp[i-3])+arr[i];
        }
        cout<<max(dp[n],dp[n-1])<<endl;
    }
}
```

### 取數字2

[題目連結](https://neoj.sprout.tw/problem/142/)
上面取數字1的進階版，可以一樣定義，不過轉移式要稍微改變（其實就只是把原本只有2的區間擴大成k）
**定義：**$dp[i]$ 為取第i個數字的的最大值
**轉移式：**
$dp[i]=max(arr[j]+arr[i]),2k\leq i \leq n,i-2k\leq j \leq i-k$
轉移式比較複雜一點，不過就是取數字1的延伸
**邊界：**
$dp[i] = arr[i],1\leq i \leq k$
$dp[i]=max(arr[j]+arr[i]),k+1\leq i \leq 2k,1\leq j \leq i-k$

邊界的話，如果要說成是轉移式也是可以啦

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0);
#define int long long
using namespace std;
int t;

signed main(){
    ios;
    cin>>t;
    while(t--){
        int n,arr[100005],k;cin>>n>>k;
        int dp[100005];
        memset(dp,0,sizeof(dp));
        memset(arr,0,sizeof(arr));

        for(int i=1;i<=n;i++)cin>>arr[i];
        //[1,k]的邊界轉移
        for(int i=1;i<=k;i++)dp[i] = arr[i];
        //[k+1,2k]的邊界轉移
        for(int i=k+1;i<=2*k;i++){
            for(int j=1;j<=i-k;j++){//看可以配上[1,k]最大的那一個
                dp[i]=max(dp[i],arr[j]+arr[i]);
            }
        }
        for(int i=2*k+1;i<=n;i++){
            for(int j = i-2*k;j<=i-k;j++){//開始轉移，掃描k次
                dp[i] = max(dp[i],dp[j]+arr[i]);
            }
        }
        cout<<max_element(dp, dp+n+1)<<endl;
    }
}
```

### 合成円円

[題目連結](https://neoj.sprout.tw/problem/143/)
開始接觸到區間DP，比單純的DP又多了一些要注意的地方
上網查關於區間DP的資料，一般都會有這些內容：

```python=
for (枚舉區間長度)
    for (枚舉左端點)
        for (枚舉分割點)
            f[l, r] = f[l, k] + f[k, r] + val(l, r)
```

主要就是，從小的區間長度開始（大的區間需要小的區間的答案），枚舉左端點可以知道區間的範圍，接著再依序枚舉在區間內分割點，把長度為n的區間長度跑完之後就完成了
有了這一個$O(n^3)$ 的演算法，就可以開始來實作

**定義：**
$dp[i][j]$ $(1 \leq i,j \leq n)$ 為合併區間 $[i,j]$ 所需要的花費
**轉移式：**
$dp[i][j] = min(dp[i][k]+dp[k+1][j]+sum_{i,j}),i \leq k \leq j$
合併區間$dp[i][k],dp[k+1][j]$的花費為兩者相加再加上區間[i,j]的總和（可以用前綴維護）
**邊界：**
$dp[i][i] = 0,1 \leq i \leq n$

這個演算法的複雜度是$O(n^3)$，之後可以用dp優化的技巧做到更快

{% note success %}
**問題：這題可不可以用Greedy？**
我們如果每一次都找兩兩相鄰相加總和最小的兩個加起來，做n-1次，是不是可以找到最佳解？（如果可以，那幹嘛還要辛苦維護$n^3$的算法XD）

Q:答案是不行，那要反例何時出現？

> 10 7 6 7

這一組測資如果用Greedy做是63，用dp做是60，可以發現到，如果數字兩兩相加相等，那先加後加的順序就很重要，有可能因為順序不對（先加中間的7跟6），導致7沒辦法跟10合併產生更加的解法！

**找反例的方法：** 這一筆測資是利用隨機生成大量的數字去找出有沒有不一樣，方法by:
![](https://i.imgur.com/rPWTsXk.png)
{% endnote %}
這一題就先這樣，不過區間DP感覺就是可以很難的東西
時間複雜度$O(n^3)$

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0);
#define int long long
using namespace std;
int t;

signed main(){
    ios;
    cin>>t;
    while(t--){
        int n,arr[105],pre[105];cin>>n;
        int dp[105][105];
        memset(pre, 0, sizeof(pre));
        memset(dp, 0x3f3f3f3f, sizeof(dp));
        for(int i=1;i<=n;i++){
            cin>>arr[i];
            pre[i] = pre[i-1]+arr[i];
            dp[i][i] = 0;
        }
        
        for(int len = 2;len<=n;len++){
            for(int i=1;i+len-1<=n;i++){//區間[i,j]長度為len，則j = i+len-1
                int j = i+len-1;
                for(int k=i;k<=j;k++){
                    dp[i][j] = min(dp[i][j],dp[i][k]+dp[k+1][j]+pre[j]-pre[i-1]);
                }
            }
        }
        cout<<dp[1][n]<<endl;
    }
}
```

## 手寫作業

這一週沒有手寫！因為下禮拜是第一階段認證考

# 資芽第八週：動態規劃（二）

這兩週真是風風雨雨，首先第一週是段考週，所以有很多時間拿去惡補段考。第二週則是疫情關係要在家裡線上上課，結果進度就辣了一大截...趕快追QQ！

## 上課內容

一堆的背包問題（真的很多種耶）

**01背包問題**
**無限背包問題**

- 先固定背包數量看價值跟先固定價值看背包數量可以互換

**有限背包問題**

- 多枚舉每個物品要放入的數量
- 拆分：把同重量的物品看成不同的物品（複雜度不變）
- 把$t_i$ 個物品拆成$\lfloor \log_2 t_i+1\rfloor$個物品
- 分成的堆數最少：$\lceil \log{t_i}+1\rceil$(因為分成 $k$ 堆最多只可能有 $2^k$ 個值)
- 複雜度：$O(n\log{max(t_i)}W)$

**混合背包問題**

- 遇到什麼樣的物品就怎麼做（對有限個背包拆分）
- 分成01背包跟無限背包做

**二維背包問題**

- 多一維度狀態轉移（可以壓掉一個維度）

**分組背包問題**

- 再多一維儲存第幾組

**背包合併**

- 直接把物品混起來做

**背包問題變化**

- (1)求最大價值的方法總數
  - 用g[i] 儲存重量i 的方法數
- (2)求最大價值的一組方案
  - g[i] 看有沒有被更新過，有就g[I] = 1，回溯找
- (3)求最大價值的字典序最小的一組方案
  - 把物品倒過來（由大到小）因為越小的(1)要最後考慮，否則1先考慮後面的大的數字會把1覆蓋掉，字典序就變大
- (4)求次大價值的解/第K大價值的解
  - 看投影片

**分數背包**

- Greedy

**不同做法複雜度**

- 用價值做狀態
- 用重量做狀態
- <font color="#f00">V, W都很大但n很小？
  - 枚舉$2^n$ <font color="#000">
- 如果V, W都很大n也蠻大？
  - 折半枚舉（meet in the middle）根號算法

## 上機作業

背包問題主要有三個變量：價值、重量、物品數量，因此可以有三個作法：

1. 以物品數量n 作為狀態，爆搜，複雜度：$O(2^n)$
2. 以價值v 作為狀態(v為物品價值總和），dp作，複雜度：$O(NV)$
3. 以重量w 作為狀態(w為物品重量上限），dp作，複雜度：$O(NW)$

### 高棕櫚農場

[題目連結](https://neoj.sprout.tw/problem/157/)
這一題不能用重量做，因為重量的範圍可以到$10^5$，因此只能用價值來做
有一個要點，無限大可以memset定義為**0x3f3f3f3f**，以十進位表示1061109567，在int的範圍但不會超過

#### 定義

定義$f(n,m)$為取n樣物品,價值恰為m,重量總和最小值

#### 轉移方式

$f(n,m) = min(f(n-1,m), f(n-1,m-v_n)+w_n), m ≧ v_n$
$f(n,m) = f(n-1,m), m < v_n$

#### 邊界條件

f(0,0) = 0, f(0,k) = INF (k>0)$
因為取零樣物品價值要k不可能達到，因此重量設為無限大

我們可以藉由滾動dp來節省空間，壓成一維（跟用重量作為狀態一樣）
最後，在從dp裡面取出max(k), for all f(N,k) ≦ W

#### 程式碼

```cpp=
#include <bits/stdc++.h>
#define int long long
#define INF 0x3f3f3f3f
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int t;

signed main(){
    ios;
    cin>>t;
    while(t--){
        int n,m,val[105],weight[100005];cin>>n>>m;
        for(int i=1;i<=n;i++)cin>>weight[i]>>val[i];
        int dp[100005];
        //定義f(n,m)取n樣物品,價值為m,重量總和最小
        //dp[i]：價值為i時,重量最小為dp[i]
        memset(dp,INF,sizeof(dp));
        dp[0] = 0;
        for(int i=1;i<=n;i++){
            for(int j=10000;j>=val[i];j--){
                dp[j] = min(dp[j],dp[j-val[i]]+weight[i]);
            }
        }
        int ans = 0;
        for(int i=0;i<=10000;i++)
            if(dp[i] <= m && i > ans)ans = i;
        cout<<ans<<endl;
    }
}
```

### 高棕櫚農場2

[題目連結](https://neoj.sprout.tw/problem/158/)
有些細節是必須要注意的，也就是初始化的細節。
{% note success %}
**背包問題是否恰好裝滿**

對於原本初始化dp[0] = 0，代表對於重量限制為0的背包價值最高為0
接下來有兩種情況需要討論，第一種是重量限制為w的背包最多的價值

**1. 恰好裝滿**
此時必須初始化dp[i] = -INF，是因為要恰好裝滿的關係，初始化的dp 數組事實上就是**在沒有任何物品可以放入背包時的合法狀態**，其他除了0之外容量的背包均沒有合法的解，屬於未定義的狀態，所以都應該被賦值為 −∞ 。當前的合法解，一定是從之前的合法狀態推得的(−∞跟−∞取max還是−∞)

**2. 不需恰好裝滿**
如果背包並非必須被裝滿，那麼任何容量的背包都有一個合法解“什麼也不裝”，這個解的價值為0,所以初始化時狀態的值也就全部為0了。

如果來看轉移式，$dp[j] = max(dp[j],dp[j-weight[i]]+val[i])$，如果兩者的狀態都屬於未定義，對於需恰好裝滿的狀況，兩者都是−∞，表示沒有合法的狀態可以構成此重量。同時，如果不需恰好裝滿的情況，即使$dp[j]$和$dp[j-weight[i]]$都未定義(等於0)，還是可以被更新（在沒有裝滿的情況下，dp[j] = val[i]）
{% endnote %}
這一題除了以上發現，還有一個很重要的東西，就是迴圈到底要放哪一層的問題。主要是卡在 for(int p=1;p<=k;p++)到底要放在哪一層的問題，結果是要放在第三層。
{% note info %}
問題一：dp[j][p]取決於dp[j][p] 和dp[j-weight[i]][p-1]，而且對於一個物品最多只能放一次，如果放在第二層，dp[j-weight[i]][p-1] 就已經被更新過了，有可能已經取了第 i 樣物品會有重複取的問題，如果放在第三層，代表對每一種不同的重量先更新放入幾樣物品的1到k，再更新重量，這樣就可以保證dp[j][p]不會取到已經更新的格子（dp[j][p] 沒被更新、dp[j-weight[i]][p-1] 其中第一維的j-weight[i] 也還沒被更新）

問題二：p要從1到k還是k到1，這其實都可以，因為要取的格子不管從前往後或後往前取都只會取到上一輪(i-1) 的更新東西，因此不影響。還有，因為是定義**最多取p樣物品**，所以無論i為多少，每一次p皆要更新的k（如果k=5，取一樣物品也符合情況）
{% endnote %}

#### 定義

定義$f(j,p)$看完 *i* 樣物品後，重量限制為j，**最多**取p樣物品的最大價值

#### 轉移方式

$dp[j][p] = max(dp[j][p],dp[j-weight[i]][p-1]+val[i])$

#### 邊界條件

$dp[i][j] = 0$ (for all elements in dp)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define INF 0x3f3f3f3f
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int t;

signed main(){
    ios;
    cin>>t;
    while(t--){
        int n,m,k,val[105],weight[10005];cin>>n>>m>>k;
        for(int i=1;i<=n;i++)cin>>weight[i]>>val[i];
        int dp[1005][105];
        memset(dp, 0, sizeof(dp));
        for(int i=1;i<=n;i++){
            for(int j=m;j>=weight[i];j--){
                for(int p=1;p<=k;p++){
                    dp[j][p] = max(dp[j][p],dp[j-weight[i]][p-1]+val[i]);
                }
            }
        }
        cout<<dp[m][k]<<endl;
    }
}
/*
 0 4 5 6 6 6
 0 4 5 6 9 10
 0 4 5 6 9 10
*/
```

### 玩電梯

[題目連結](https://neoj.sprout.tw/problem/416/)
[題目連結2](https://codeforces.com/problemset/problem/479/E)

這一題要用到3個重要的技巧：前綴和、差分、滾動dp
差分在某一次手寫作業有寫到，不過那時候沒有很注意這個部分就是了
{% note info %}
**差分**

差分是前綴和的逆運算，也就是說，把兩項的差算出來就是差分。定義如下：
$$b_i = \begin{cases}a_i-a_{i-1}, &\text{if }i\gt 1 \\a_1, & \text{if } i = 1\end{cases}$$

差分的使用時機是區間加值，一個區間內的數字都加上一個定值，這時候就可以使用到差分的技巧。使用方式如下，當我要在區間 $[l,r]$ 的每一個數字都加上一個值$v$，以下步驟：

1. 定義一個新的陣列 $b_i$ 表示每一項差分
2. 設 $b_l = b_l+v$，$b_{r+1} = b_{r+1}-v$
3. 將差分的每一項加上前一項，即為原數列 $b_i = b_{i-1}+b_i$

第二步驟可以重複好幾次做，這樣複雜度從原本的$O(n)$就變成了O(1)了！
{% endnote %}

這一題使用到差分的技巧，讓原本的$O(kn^2)$減少成$O(kn)$，然後就可以過了！

#### 定義

定義 $dp[i][j]$ 為第 i 次走到樓層j的方法數

#### 轉移式

這題如果用拉的比較不好想，所以改用推的試試看
$dp[i+1][j] = dp[i+1][j]+dp[i][p],$ for $j\in[p-r],[p+1,p+r],r = |p-b|-1$

#### 邊界條件

$dp[0][a] = 1$

轉移式比較複雜一點，不過可以用差分優化搭配前綴和把原本$O(n)$的時間降到$O(1)$
從這一題可以發現到，用拉的和用推的有不同的使用時機，可以以思考方式比較清楚的想法去想轉移式。

```cpp=
#include <bits/stdc++.h>
#define int long long
#define mod 1000000007
#define ios ios::sync_with_stdio(0)
using namespace std;
int n,a,b,k,dp[2][2005];

int modify(int x){
    return (x%mod+mod)%mod;
}

void sec(int l,int r,int v,int id){
 
    l = max(l,(long long)1);
    r = min(r,n);
    dp[(id+1)%2][l] = modify(dp[(id+1)%2][l]+v);
    dp[(id+1)%2][r+1] = modify(dp[(id+1)%2][r+1]-v);
}

signed main(){
    ios;
    cin>>n>>a>>b>>k;

    memset(dp,0,sizeof(dp));

    dp[0][a] = 1;
    for(int s=0;s<k;s++){  //每一次電梯移動
        for(int i=1;i<=n;i++){ //每一樓層轉移
            int d = abs(b-i)-1;
            sec(i-d,i-1,dp[s%2][i],s);
            sec(i+1,i+d,dp[s%2][i],s);
        }
        for(int i=1;i<=n;i++){
            dp[(s+1)%2][i] += dp[(s+1)%2][i-1];
            dp[(s+1)%2][i] = modify(dp[(s+1)%2][i]);
        }
        for(int i=1;i<=n;i++)dp[(s)%2][i] = 0;
    }

    int ans = 0;
    for(int i=1;i<=n;i++){
        ans+=dp[k%2][i];
        ans = modify(ans);
    }
    cout<<ans<<endl;
}

//8樓、2樓開始、5樓不能去、2次電梯
```

### 取名字好困難QQ

[題目連結](https://neoj.sprout.tw/problem/421/)
跟題目一樣，我覺得要通靈才能想到這一題的作法！
結果是問了別人才大概感受到這一種作法！！！
![](https://i.imgur.com/EmxSHqM.png)

我們既然不知道到底一個數字要不要乘2，我們可以透過做LIS的過程來做決定。當我們把乘與2之後的數字跟原本數字一起push進去，就可以發現到LIS不可能同時取到2個數字。利用這個方法就可以用LIS的過程決定一個數字到底應該要變2倍還是不用。
要找到最長的非嚴格遞增序列，最大的差別就是要把原本的lower_bound改成upper_bound。一整天想一題的感覺超級糟糕QQ

```cpp=
#include <bits/stdc++.h>
#define int long long
#define mod 1000000007
#define FOR(i,n) for(int i=0;i<n;i++)
#define ios ios::sync_with_stdio(0)
using namespace std;
int t;

void solve(){
    int n,m;cin>>n>>m;
    vector<int>vec;
    FOR(i,n){
        int temp;cin>>temp;
        if(2*temp<m)continue;
        else if(temp<m){
            temp*=2;
            vec.push_back(temp);
        }
        else{
            vec.push_back(2*temp);
            vec.push_back(temp);
        }
    }
    if(vec.size()<1){
        cout<<0<<endl;
        return;
    }
    
    //正常做LIS
    vector<int> lis;int len = vec.size();
    lis.push_back(vec[0]);
    for(int i=1;i<len;i++){
        if(lis.back()<=vec[i])lis.push_back(vec[i]);
        else *upper_bound(lis.begin(),lis.end(),vec[i]) = vec[i];
    }
    cout<<lis.size()<<endl;
}

signed main(){
    ios;
    cin>>t;
    while(t--){
        solve();
    }
}

```

## 手寫作業

這一週是講歸約法，作業如下：
![](https://i.imgur.com/amWVYo0.jpg)
這一週手寫作業的狀況還不錯，71/75，不過第一題是緊急向別人求救才把答案改掉
![](https://i.imgur.com/GMtrB99.png)
原本是寫B，後來改A，原因有以下兩點：

1. 因為函數在 return 時不會把 stack memory 上的資料清空，所以所有的區域變數都會被留在 stack memory 上，其他函數可能會共用到同一塊記憶體空間。
2. 有可能會對非法的陣列位址取值(RE的情況)，而存取到別的函數的區域變數。

所以A是錯誤的！
下面是解答的畫法，其實跟我蠻像的XDD
![](https://i.imgur.com/NyN0Kwy.png)
