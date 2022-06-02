---
title: 資芽第四週：枚舉算法
date: 2021-3-27
tags: 
    - 資芽筆記
    - 枚舉
categories: 資訊之芽筆記
mathjax: true
---


## 上課內容

這一週的主題是**枚舉(enumeration)**，內容主要環繞二分搜、三分搜、DFS剪枝這些部分。
這一週的內容算是相對輕鬆，解數獨的部分也是蠻有趣的！
<!--more-->

## 上機作業

### 數獨

[題目連結](https://neoj.sprout.tw/problem/62/)
首先是數獨，很有趣，之前有做過類似的是**八皇后問題**
用到**DFS剪枝**，[筆記在這](https://peienwu.com/2021/03/07/Sudoku/)

```cpp=
#include <bits/stdc++.h>
using namespace std;

class Sudoku{
private:
    int maze[9][9];
    bool flag = false;
public:
    void print(string s);//
    void scan_maze();//
    vector<int> select(int, int);
    int next_empty(int, int);
    void solving();
    void dfs(int row, int col );
    
};
void Sudoku::print(string s){
    cout<<endl<<"====="<<s<<"======"<<endl;
    for (int i=0; i<9;i++){
        cout<<"|";
        for (int j=0;j<9;j++){
            cout<<maze[i][j];
            if(j%3==2)cout<<"|";
        }
        cout<<endl;
        if(i%3==2)cout<<"-------------"<<endl;
    }
}
vector<int> Sudoku::select(int r, int c){
    bool box[9]={0};
    for(int i=0;i<9;i++){
        if(maze[i][c]>0)box[maze[i][c]-1] = 1;
        if(maze[r][i]>0)box[maze[r][i]-1] = 1;
    }
    int row_start = 3*(r/3),col_start = 3*(c/3);
    for(int i=0;i<3;i++){
        for(int j=0;j<3;j++){
            if(maze[row_start+i][col_start+j]>0)
                box[maze[row_start+i][col_start+j]-1] = 1;
        }
    }
    vector<int> ans;
    for(int i=0;i<9;i++)
        if(!box[i])ans.push_back(i+1);
    return ans;
}
int Sudoku::next_empty(int row, int col){
    int ind = col;
    for(int i=row;i<9;i++){
        while(ind<9){
            if(maze[i][ind]==0){
                int pos = i*9+ind;
                return pos;
            }
            ind++;
        }
        ind = 0;
    }
    return -1;
}
void Sudoku::dfs(int row, int col ){
    int pos = next_empty(row, col),nr,nc;
    if(pos == -1){
        flag = true;
        return;
    }
    vector<int> candidate = select(row, col);
    int len = candidate.size();
    
    for(int i=0;i<len;i++){
        maze[row][col] = candidate[i];
        pos = next_empty(row, col);
        nr = pos/9; nc = pos%9;
        dfs(nr, nc);
        if(flag)return;
    }
    maze[row][col] = 0;
}
void Sudoku::scan_maze(){
    for(int i=0;i<9;i++){
        for(int j=0;j<9;j++){
            char temp;cin>>temp;
            if(temp=='.')maze[i][j] = 0;
            else maze[i][j] = temp -'0';
        }
    }
}
void Sudoku::solving(){
    int first_empty = next_empty(0, 0),nr,nc;
    nr = first_empty/9; nc = first_empty%9;
    dfs(nr, nc);
    bool f = false;
    for(int i=0;i<9;i++){
        for(int j=0;j<9;j++){
            vector<int> v = select(i,j);
            if(v.size()>0)f = true;
        }
    }
    if(!flag || f)cout<<"No solution."<<endl;
    else{
        print("solved");
    }
    flag = false;
}
int main(){
    Sudoku sodoku1;
    sodoku1.scan_maze();
    sodoku1.print("begin");
    sodoku1.solving();
}
```

### Lotto(UVA)

[題目連結](https://neoj.sprout.tw/problem/63/)
這一題主要就是建立一個排列樹，把每一種可能的情況用遞迴列出來
![](https://i.imgur.com/cKGOtgP.png)
這一題用DFS明顯比BFS來得好、**空間**省很多（用一下資芽的圖XD)
![](https://i.imgur.com/sOjMiuk.png)

來看程式碼吧！

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n,arr[15];

void permutation(int arr_ind, int cur[],int cur_ind){
    if(arr_ind>n)return;
    if(cur_ind>=6){
        for(int i=0;i<6;i++){
            if(i>0)cout<<" ";
            cout<<cur[i];
        }
        cout<<endl;
        return;
    }
    for(int i = 0; i <= n-(6-cur_ind); i++){
        cur[cur_ind] = arr[arr_ind+i];
        permutation(arr_ind+i+1, cur, cur_ind+1);
    }
}

bool f=0;

int main(){
    ios;
    while(cin>>n){
        if(n==0)break;
    if(f)cout<<endl;
    memset(arr, 0, sizeof(arr));
    for(int i=0;i<n;i++)cin>>arr[i];
    sort(arr, arr+n);
    int cur[10];
    permutation(0, cur, 0);
    f=1;
    }
}
```

{% note warning %}
這一題必須很小心輸入輸出，它們會讓你WA很久呀！

1. 行末不要多餘空白
2. 測資全部印出來後，末端**不要多餘換行**

大概就這兩點
{% endnote %}

### 田忌賽馬(NPSC)

[題目連結](https://neoj.sprout.tw/problem/69/)
之前有寫過這一題，不過現在再寫一次還是WA了很久，對於二分搜的維護（到底是取到0還是1）還要更熟悉
除此之外，我因為邊界問題（$10^8$ 或是$10^8+1$）沒有仔細注意就一直WA

```cpp=
#include <bits/stdc++.h>
#define N 10000 
using namespace std;  
lli n,k,t,M,upper = 100000000,lower = -1;  
lli mine[N],develop[N],enemy[N],curr[N];  

bool win(lli day){  
    int it = 0,sum = 0;  
    for(int i=0;i<n;i++)  
        curr[i] = mine[i]+day*develop[i];  
    sort(curr, curr+n);  
    for(int i=0;i<n;i++){  
        if(curr[i]>enemy[it]){  
            sum++;  
            it++;  
        }  
    }  
    return (sum>=k);  
}  
  
int main(){  
    ios::sync_with_stdio(false);  
    cin.tie(0);  
    cin>>t;  
    while(t--){  
        upper = 100000000;lower = -1;  
        cin>>n>>k;  
        for(int i=0;i<n;i++){  
            cin>>mine[i]>>develop[i];  
        }  
        for(int i=0;i<n;i++){  
            cin>>enemy[i];  
        }  
        sort(enemy, enemy+n);  
        if(!win(upper))cout<<-1<<endl;  
        else{  
            while(upper-lower!=1){  
                M = (upper+lower)/2;  
                if(win(M))upper = M;  
                else lower = M;  
            }  
            cout<<upper<<endl;  
        }  
    }  
}  
```

{% note success %}
這樣維護二分搜也可以，就是保證 r 一定是1（l就不一定）

```cpp=
while(r>l){
    int mid = (l+r)/2, arr[n+5];
    for(int i=0;i<n;i++)arr[i] = mine[i]+mid*rate[i];
        sort(arr, arr+n,greater<int>());
        int f = can_win(arr, enemy);
        if(f)r = mid;
        else l = mid+1;
    }
```

單純二分搜，找01分界點！
{% endnote %}

### Happiness Function(2013 台清交程式設計競賽)

[題目連結](https://neoj.sprout.tw/problem/72/)
很酷的題目，首先必須做一點數學上的分析
{% note info %}
假設題目給定的n個二次函數依序為$A_1(x),A_2(x),...,A_n(x)$
令函數$f(t)=max(A_j(t), 1<=j<=n)$
則$f(t)$為一個U型函數
{% endnote %}
有了這個性質之後就可以利用**三分搜**來找U型函數的最小值!

值得注意的是：

1. *float* 是**單精度**浮點數、*double* 是**雙精度**浮點數，我用 float吃了好幾個WA，結果用double 直接 AC
2. while 迴圈裡面的判斷不要用浮點數相減，會出事（TLE）

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define float a
using namespace std;
int T,n;
double a[12],b[12],c[12];

double func(double t, int i){
    return a[i]*(t-b[i])*(t-b[i])+c[i];
}

int main(){
    ios;
    cout<<fixed<<setprecision(5);
    cin>>T;
    while(T--){
        cin>>n;
        for(int i=0;i<n;i++)cin>>a[i]>>b[i]>>c[i];
        double left = 0.0, right = 300.0;
        double now_l = 0,now_r = 0,nl = 0,nr = 0;
        int cnt = 0;
        while(cnt<=10000){
            nl = (2*left+right)/3;
            nr = (2*right+left)/3;
            now_l = now_r = 0;
            for(int i=0;i<n;i++)now_r = max(now_r, func(nr,i));
            for(int i=0;i<n;i++)now_l = max(now_l, func(nl,i));
            if(now_r > now_l)right = nr;
            else left = nl;
            cnt++;
        }
        cout<<now_l<<endl;
    }
}
```

簡單統整，就是三分搜找最低點！

### 東方古墓古文

[題目連結](https://tioj.ck.tp.edu.tw/problems/73)
這一題主要是二分搜工作量上限X，寫一個函數判斷是否工作量X是否可行
Debug 超久，不知道在幹嘛，結果最後是卡在二分搜的上界範圍不夠大
要開到$10^9$這麼大！
時間複雜度：$N$$log$(總工作量)

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0);
#define int long long
using namespace std;
int t;
int n,m,arr[100005];

bool is_ok(int lim){
    int sum = 0,ans = 1;
    for(int i=0;i<n;i++){
        if(arr[i]>lim)return 0;
        sum += arr[i];
        if(sum+arr[i+1] > lim){
            sum = 0;
            ans++;
        }
    }
    if(ans>m)return 0;
    else return 1;
}

signed main(){
    ios;
    cin>>t;
    while(t--){
        cin>>n>>m;
        memset(arr, 0, sizeof(arr));
        for(int i=0;i<n;i++)cin>>arr[i];
        arr[n] = -INT_MAX;
        int r = 1000000001,l = 0;
        while(r-l > 1){
            int mid = (l+r)/2;
            if(is_ok(mid))r = mid;
            else l = mid;
        }
        if(r == 1000000001)cout<<1000000000<<endl;
        else cout<<r<<endl;
    }
}
```

### 極速馬拉松

[題目連結](https://tioj.ck.tp.edu.tw/problems/60)
這一題難的主要是變數太多
只要理解它到底要幹嘛，就可以發現可以利用$O(N)$枚舉出固定去間內最大的距離
之後再$O(logN)$ 二分搜時間
就可以用$O(logN)$ AC這一題

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0);
#define int long long
using namespace std;
int a,b,c,d,m,s,t;

int func(int time){
    int speed_time = m/c,max_distance = 0,cur_m = m%c;
    int cur_distance = b*speed_time,cur_t = speed_time;

    if(cur_t<time){
        int len = time-cur_t;
        for(int i=0;i<=len;i++){//時限內最多可以跑多遠
            cur_distance = b*speed_time;
            cur_t = speed_time+i;
            cur_m = (m%c)+d*i;

            int step = min(cur_m/c,time-cur_t);

            cur_m = cur_m-step*c;
            cur_t+=step;
            cur_distance += b*step;

            cur_distance+=a*(time-cur_t);
            max_distance = max(max_distance,cur_distance);
        }
    }
    else max_distance = b*time;
    return max_distance;
}

signed main(){
    ios;
    cin>>a>>b>>c>>d>>m>>s>>t;
    int max_distance = func(t);
    if(s>=max_distance)cout<<"No"<<endl<<max_distance<<endl;
    else{
        cout<<"Yes"<<endl;
        int r = t,l = 0;
        while(r-l>1){
            int mid = (r+l)/2;
            if(func(mid)>s)r = mid;
            else l = mid;
        }
        cout<<r<<endl;
    }
}
```

### 人生低潮

[題目連結](https://tioj.ck.tp.edu.tw/problems/1926)

## 手寫作業

手寫作業介紹字串比對、各種排序演算法。這是我算是第一次搞懂快速排序在幹嘛，不然之前都是看兩個指針在指來指去不知道確切的功能是什麼。
除了快排之外，基數排序也是第一次看到，原來排序可以做到**線性時間複雜度！**
大開眼界！（不過只是用於整數排序）
這次手寫覺得有些困難，不過學到了很多東西！（可以來做一個各種排序演算的筆記）
