---
title: 字串演算法例題 (String)
date: 2021-9-5
tags: 
    - 字串
    - 演算法
categories:
	- C++進階主題
	- 字串演算法

mathjax: true
password: sprout2022
---

## 題目目錄

- NEOJ 266 溫力的故事
- NEOJ 265 欸迪的字串
- NEOJ 267 自動完成系統
- NEOJ 264 欸迪的字串
- ZJ d518: 文字抄寫 II
- TIOJ 1306 字串中的字串
- TIOJ 1321 好多回文 ndromePali
- ZJ d978: 最长回文字串
- TIOJ 1725 Massacre at Camp Happy
- TIOJ 1497 喝醉的宿主 The drunk host
- TIOJ 1515 Problem E. 似曾相識
<!-- more -->

### NEOJ 266 溫力的故事

[題目連結](https://neoj.sprout.tw/problem/266/)
[Submission](https://neoj.sprout.tw/challenge/179031/)
> 題目敘述
> 給你n個字串m筆詢問一個字串，對每一筆詢問輸出詢問在n個字串中出現的次數。

這一題在**隨機算法**做過，今天用字典樹Trie做一次。在隨機算法中，透過Rolling Hash的公式，對每一個字串生成一個值，利用這個值查詢出現的次數。如果我們用Trie的話，則是建立一棵指標樹，透過走法這一棵字典樹得知詢問字串出現的次數！

#### 程式碼

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld long double
#define N 2000
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int n,m;

struct Trie{        //利用指標建立一棵樹
    Trie* c[26];    //對應a-z每一條邊
    int cnt;        //字串出現次數
    Trie(): cnt(0){ //初始設定
        memset(c,0,sizeof(c));
    }
};
Trie* root = new Trie();

int ch(char temp){
    return temp-'a';
}

void insert(char *s){
    Trie *ptr = root;           //從根節點尋找
    while(*s){
        if(!ptr->c[ch(*s)])     //如果樹上無此字元則new
            ptr->c[ch(*s)] = new Trie();
        ptr = ptr->c[ch(*s)];   //繼續造訪Trie
        s += 1;                 //字串下一個字元
    }
    ptr->cnt += 1;              //字串出現次數(字串對應唯一葉節點)
}

int find(char *s){              //查找字串s
    Trie *ptr = root;           //根節點尋找
    while(*s){                  //無此字串，回傳次數0
        if(!ptr->c[ch(*s)])return 0;
        ptr = ptr->c[ch(*s)];
        s += 1;                 //字串下一個字元
    }
    return ptr->cnt;            //回傳字串出現次數
}

signed main(){
    Orz;
    cin>>n>>m;
    rep(i,0,n-1){
        char s[105];cin>>s;
        insert(s);
    }
    rep(i,0,m-1){
        char s[105];cin>>s;
        cout<<find(s)<<endl;
    }
}
```

### NEOJ 265 欸迪的字串

[題目連結](https://neoj.sprout.tw/problem/265/)
[Submission](https://neoj.sprout.tw/challenge/179036/)
> 題目敘述
> 給你字串S和字串T，求出S在T中出現的位置

這一題在**隨機演算法**也有出現過，當時是用Rolling Hash的方式透過扣掉Hash的方法比對字串是否相同（推一下公式就知道）。現在要用的是**KMP演算法**，很複雜不好想。

比較一下隨機跟KMP的時間，下方為KMP，上方則是隨機算法，兩者時間差了將近1倍，雖然兩者都可以在O(|T|)時間內進行匹配，但是建立Rolling Hash的常數是比較大的！
![](https://i.imgur.com/a7CPjyi.png)

#### 程式碼

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld long double
#define int long long
#define N 500005
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int n,m;

vector<int> KMP_match(char *S,int *F,char *T){
    vector<int> ans;            //回傳匹配相同地方
    int p = -1;                 //紀錄短字串有多少被匹配
    for(int i=0;T[i];i++){      //每一迴圈都讓T[i]被匹配到
        while(p!=-1 && S[p+1]!=T[i])
            p = F[p];           //使T[i]一定可以被匹配到
        if(S[p+1] == T[i])
            p += 1;             //T的第i個與S的p+1可以匹配
        if(!S[p+1]){            //S[p]已經匹配完成
            ans.push_back(i-p); //回推匹配開頭
            p = F[p];           //繼續下一輪匹配
        }
    }
    return ans;
}

//O(|S|)要配對的字串以及Fail Function
void KMP_build(char *S,int *F){
    int p = F[0] = -1;              //初始設定為-1
    for(int i=1;S[i];i++){          //1到接下來字元
        while(p!=-1 && S[p+1]!=S[i])
            p = F[p];               //無法繼續配對，尋找更短字串
        if(S[p+1] == S[i])          //配對成功(如都沒有一樣的就-1)
            p += 1;
        F[i] = p;                   //設定F[i]
    }
}

signed main(){
    Orz;
    char S[N],T[N];     //詢問短字串、長在串
    int F[N];           //失敗函數
    cin>>S>>T;
    KMP_build(S,F);
    vector<int> ans = KMP_match(S,F,T);
    if(ans.size()>0){
        cout<<ans[0];
        rep(i,1,ans.size()-1)cout<<" "<<ans[i];
    }
    cout<<endl;
}
```

### NEOJ 267 自動完成系統

[題目連結](https://neoj.sprout.tw/problem/267/)
[Submission](https://neoj.sprout.tw/challenge/179148/)
> 題目敘述
[點這裡](https://www.facebook.com/codingcompetitions/hacker-cup/2015/round-1/problems/B)有FB Hacker Cup的原題連結，簡單來說就是想像手機的自動填入系統，每加入一個字串會記錄到資料庫中，當資料庫裡面沒有相同前綴的字串時就輸出前綴長度。

用字典樹Trie插入每一個字串，插入過程中返回從頭到開始new新的節點之間經過的節點樹，代表需要輸入多少個字元才能觸發自動完成系統。這一題是基礎的Trie應用。

#### 程式碼

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld long double
#define N 1000005
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int t,n;
char S[N];

int ch(char a){
    return a-'a';
}

struct Trie{
    Trie* c[26];
    Trie(){
        memset(c,0,sizeof(c));
    }
};
Trie *root = new Trie();

int insert(char *S){
    Trie *ptr = root;
    int step = 0,f = 1;
    while(*S){
        if(f)step++;
        if(!ptr->c[ch(*S)]){
            ptr->c[ch(*S)] = new Trie();
            f = 0;
        }
        ptr = ptr->c[ch(*S)];
        S++;
    }
    return step;
}
void clear(Trie *s){
    for(int i=0;i<26;i++){
        if(s->c[i]){
            clear(s->c[i]);
            delete s->c[i];
            s->c[i] = nullptr;
        }
    }
}

int main(){
    cin>>t;
    int cnt = 0;
    while(t--){
        cnt++;
        cin>>n;
        int step = 0;
        rep(i,0,n-1){
            cin>>S;
            step +=insert(S);
        }
        cout<<"Case #"<<cnt<<": "<<step<<endl;
        clear(root);
    }
}
```

### NEOJ 264 欸迪的字串

[題目連結](https://neoj.sprout.tw/problem/264/)
[Submission](https://neoj.sprout.tw/challenge/179060/)
> 題目敘述
> 給你字串S，求一個最短字串T的長度，滿足經由複製若干次後接起來會是字串S。

這一題在一開始看到感覺好難，不過在慢慢理解 Z_Algorithm 之後，在講義中發現到這個問題也可以用這個演算法來解決！因為Z函數是滿足從i開始的最長前綴，因此當 $i$ 為 字串長度 $n$ 的因數的時候，只要 $i+Z[i+1]=n$ 成立，就必定可以用 $S[1:i]$ 組成字串 $S$ 。實作上要注意字串是0-base，i+1就相等於字串第i個位置，處理上要特別注意！

#### 程式碼

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld long double
#define N 500005
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int n,m;

void Z_algo(char *S,int *Z){
    int l = 0,r = 0;
    Z[0] = 0;
    for(int i=1;S[i];i++){
        Z[i] = max(0,min(Z[i-l],r-i));
        while(S[Z[i]]&&S[Z[i]] == S[i+Z[i]]){
            l = i;r = i+Z[i];
            Z[i]++;
        }
    }
}

signed main(){
    Orz;
    char s[N];cin>>s;
    int Z[N],n = strlen(s),ans = n;
    Z_algo(s,Z);
    
    rep(i,1,n-1){
        if(n%i==0 && i+Z[i]==n){
            ans = i;
            break;
        }
    }
    cout<<ans<<endl;
}
```

### ZJ d518: 文字抄寫 II

[題目連結](https://zerojudge.tw/ShowProblem?problemid=d518)
> 題目敘述
若這個字串之前已經出現過，則輸出的出現號碼，若沒有則輸出它將被編寫的號碼.

裸題Trie，不過要注意每一次要重置，將所有的實體Delete掉。

#### 程式碼

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld long double
#define N 110
#define Orz ios::sync_with_stdio(0),cin.tie(0),cout.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int n,m;

struct Trie{
    Trie* c[26];
    int ind;
    Trie(): ind(0){
        memset(c,0,sizeof(c));
    }
};
Trie* root = new Trie();

int ch(char temp){
    return temp-'a';
}
void clear(Trie *cur){
    for(int i=0;i<26;i++){
        if(cur->c[i]){
            clear(cur->c[i]);
            delete[] cur->c[i];
            cur->c[i] = NULL;
        }
    }
}

void insert(char *s,int num){
    Trie *ptr = root;           //從根節點尋找
    while(*s){
        if(!ptr->c[ch(*s)])     //如果樹上無此字元則new
            ptr->c[ch(*s)] = new Trie();
        ptr = ptr->c[ch(*s)];   //繼續造訪Trie
        s += 1;                 //字串下一個字元
    }
    ptr->ind = num;
}

int find(char *s){              //查找字串s
    Trie *ptr = root;           //根節點尋找
    while(*s){                  //無此字串，回傳次數0
        if(!ptr->c[ch(*s)])return 0;
        ptr = ptr->c[ch(*s)];
        s += 1;                 //字串下一個字元
    }
    return ptr->ind;
}

signed main(){
    while(scanf("%d",&n)!=EOF){
        int ans,cur_id = 1;
        rep(i,1,n){
            char s[N];scanf("%s",s);
            ans = find(s);
            if(ans == 0){
                printf("New! %d\n",cur_id);
                insert(s,cur_id);
                cur_id += 1;
            }
            else{
                printf("Old! %d\n",ans);
            }
        }
        clear(root);
    }
}
```

### TIOJ 1306 字串中的字串

[題目連結](https://tioj.ck.tp.edu.tw/problems/1306)
[Submission](https://tioj.ck.tp.edu.tw/submissions/263572)
> 題目敘述
裸字串匹配。對於每個詢問輸出 $S$ 在 $T$ 中出現過幾次。

裸KMP字串匹配，統計出現次數。可以嘗試不要看資料自己寫一次，會更了解KMP算法！

#### 程式碼

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld long double
#define int long long
#define N 10005
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int t,n,F[N];;
char T[N],S[N];

int KMP_match(char *S,char *T,int *F){
    int p = -1,ans = 0;
    for(int i=0;T[i];i++){
        while(p!=-1 && S[p+1]!=T[i])
            p = F[p];
        if(S[p+1] == T[i])
            p += 1;
        if(!S[p+1]){
            ans += 1;
            p = F[p];
        }
    }
    return ans;
}

void KMP_build(char *S,int *F){
    int p = F[0] = -1;
    for(int i=1;S[i];i++){
        while(p!=-1 && S[p+1]!=S[i])
            p = F[p];
        if(S[p+1] == S[i])
            p += 1;
        F[i] = p;
    }
}

void solve(){
    while(n--){
        memset(F,0,sizeof(F));
        cin>>S;
        KMP_build(S,F);
        cout<<KMP_match(S,T,F)<<endl;
    }
}

signed main(){
    Orz;
    cin>>t;
    while(t--){
        cin>>T>>n;
        solve();
    }
}
```

### TIOJ 1321 好多回文 ndromePali

[題目連結](https://tioj.ck.tp.edu.tw/problems/1321)
[Submission](https://tioj.ck.tp.edu.tw/submissions/263962)
> 題目敘述
給一個長度 ≤ $10^6$ 的字串 A，問有幾種把該字串的某前綴搬到最後面的方法，使得最後的字串為回文。
(提示:可以用類似 Z 函數的概念計算出以某字元為中心的最長回文長度。)

這一題搞得我快掛了QQQ。為什麼呢？看看以下照片就知道了：

![](https://i.imgur.com/i8Gx5Yo.png)

根據上面的「提示」，要用類似Z函數的概念把以某點為中心的最長回文算出來。一個Naive裸的作法，把用 $O(n)$ 枚舉每一個點為分割點，把分割點前面的子字串接到後面，用 $O(n)$ 的時間看是否為一回文字串。如此一來時間複雜度為 $O(n^2)$ ，對這一題來說顯然是不可行的，因為字串長度達到 $10^6$！

當我們用Manacher’s Algorithm $O(n)$ 的時間每一個字元的最大回文之後，就可以枚舉每一個可以作為分割點的地方，檢查回文的狀況。討論分割點在前半部分的情況下，也就是把短的（長度小於一半）前綴搬到後面的狀況。當分割的索引值為 $i$ ，必須保證 $s[0:i]$ 跟 $s[i:2i]$ 對稱，移到字串尾巴才能成為回文。除此之外，也必須滿足 $Z[mid+i] = mid-i$ 的情況，以就是剩下不是分割點兩側的字串必須是回文，把前綴接到後面之後才能對稱！

實作上很多細節，我DEBUG超久最後才想到不能枚舉所有字元，會出事（就像上面的三色WA），只要枚舉以 $'.'$ 為分割點的情況（分割在字元上就不行），還有很多細節，WA很多次才會知道XD

#### 程式碼

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld long double
#define N 1000005
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int n,m,Z[2*N];
bool ans[2*N];
char S[2*N],T[N];
//T原字串、S插入點字串

void Longest(){
    n = strlen(T);m = 2*n+1;
    memset(S,'.',m);
    for(int i=0;i<n;i++)S[2*i+1] = T[i];
    Z[0] = 1;
    int l = 0,r = 0;
    for(int i=1;i<m;i++){
        Z[i] = max(min(Z[2*l-i],r-i),1);
        while(i-Z[i]>=0 && i+Z[i] < m && S[i+Z[i]]==S[i-Z[i]]){
            l = i;r = i+Z[i];
            Z[i]++;
        }
    }
}

signed main(){
    Orz;
    cin>>T;
    Longest();
    
    for(int i=0;i<m;i++)Z[i]--;
    memset(ans,0,sizeof(ans));
    
    int mid = (m-1)/2,i;
    
    if(Z[mid] == mid)ans[0] = 1;
    for(i=2;i<mid;i+=2){
        if(Z[i] == i && Z[mid+i] == mid-i){
            ans[(i/2)] = 1;
        }
    }
    for(;i<m;i+=2){
        if(Z[i] == m-i-1 && Z[i-mid] == i-mid){
            ans[(i/2)] = 1;
        }
    }
    
    int sum = 0;
    for(int i=0;i<n;i++)if(ans[i])sum++;
    if(sum == 0)cout<<"none"<<endl;
    else{
        cout<<sum<<":";
        for(int i=0;i<n;i++)if(ans[i])cout<<" "<<i;
        cout<<endl;
    }
}
```

### ZJ d978: 最长回文字串

[題目連結](https://zerojudge.tw/ShowProblem?problemid=d978)
> 題目敘述
題目即題意，給你一個字串，求出最長的回文字串長度為何？

一題LPS的裸題，用Manacher's Algorithm可以將時間複雜度做到 $O(n)$！

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld long double
#define N 500005
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int n,m,Z[2*N],t;
bool ans[2*N];
char S[2*N],T[N];
//T原字串、S插入點字串

void Longest(){
    n = strlen(T);m = 2*n+1;
    memset(S,'.',m);
    for(int i=0;i<n;i++)S[2*i+1] = T[i];
    Z[0] = 1;
    int l = 0,r = 0;
    for(int i=1;i<m;i++){
        Z[i] = max(min(Z[2*l-i],r-i),1);
        while(i-Z[i]>=0 && i+Z[i] < m && S[i+Z[i]]==S[i-Z[i]]){
            l = i;r = i+Z[i];
            Z[i]++;
        }
    }
}

signed main(){
    Orz;
    cin>>t;
    while(t--){
        cin>>T;
        Longest();
        int ans = 0;
        for(int i=0;i<m;i++){
            int z = (Z[i]-1);
            ans = max(ans,z);
        }
        cout<<ans<<endl;
    }
}
```

### TIOJ 1725 Massacre at Camp Happy

[題目連結](https://tioj.ck.tp.edu.tw/problems/1725)
[Submission](https://tioj.ck.tp.edu.tw/submissions/264017)

> 題目敘述：
定義字串 A 和 B「k-幾乎相同」代表把字串 A 的前 k 字元搬到最後面時，與 B 恰有一個字元相異。給你兩個長度 ≤ $10^6$ 的字串 A 和 B，求所有使 A 和 B「k-幾乎相同」成立的 k 值。

這一題好特別，一樣要用 $O(n)$ 的時間求出答案，對於每一個操作將前k個字元搬到後面，只能用 $O(1)$ 算出來到底符不符合一字元相異的條件。
以兩個相等長度的字串 $A,B$ 為例，長度為 $l$，假設 $A=aabba,B = aacba$，兩者相差一個字元的情況下，必定會滿足$l = LCP(A,B)+LCP(rev(A),rev(B))+1$，其中rev函數表示reverse。

我們要做的就是在 $O(1)$ 的時間內求出LCP，因為題目的字串 $A$ 要求不同的起始位置，因此我們假設一個非常特別的字串 $B@AA$ ，其中@為任意沒出現過的字元。只要計算這一個字串的Z函數，就表示了 $A$ 和 $B$ 的最長共同前綴，需要兩個A的原因是因為模擬不同的k-位移下情形。

![](https://i.imgur.com/5Ir8Hjt.png)

{% note success %}
**實作小細節**
程式碼的第42行可以試著推推看逆序之後的尾巴的索引值為何。我是利用列出幾個數字之後推出來的，不過應該可以寫成比較嚴謹的數學證明！

```cpp=
Z1[n+1+i]+Z2[2*n-i+1]+1 == n
```

{% endnote %}

#### 時間複雜度

$O(n)$ 建立z函數，$O(n)$ 枚舉每一個起始點，總時間為 $O(n)$。

#### 程式碼

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld long double
#define N 1000005
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int n,m,Z1[3*N],Z2[3*N];
char A[3*N],B[3*N];

void Z_algo(int *z,char *S){
    int l = 0,r = 0;
    z[0] = 0;
    for(int i=1;i<m;i++){
        z[i] = max(min(z[i-l],r-i),0);
        while(S[i+z[i]] && S[z[i]] == S[i+z[i]]){
            l = i;r = i+z[i];
            z[i]++;
        }
    }
}

signed main(){
    Orz;
    cin>>n>>A>>B;
    m = 3*n+1;
    B[n] = '@';
    //正常序列求Z value
    rep(i,n+1,2*n)B[i] = A[i-n-1];
    rep(i,2*n+1,3*n)B[i] = A[i-2*n-1];
    Z_algo(Z1,B);
    //逆序求Z value
    reverse(B,B+n);reverse(B+n+1,B+m);
    Z_algo(Z2,B);
    
    vector<int> vec;
    for(int i=0;i<n;i++){
        if(Z1[n+1+i]+Z2[2*n-i+1]+1 == n)vec.push_back(i);
    }
    if(vec.size()){
        cout<<"TAK"<<endl;
        for(auto i:vec)cout<<i<<" ";
        cout<<endl;
    }
    else cout<<"NIE"<<endl;
    
}
//[0,n-1][n,n][n+1,2n][2n+1,3n]
```

### TIOJ 1497 喝醉的宿主 The drunk host

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

### TIOJ 1515 Problem E. 似曾相識

[題目連結](https://tioj.ck.tp.edu.tw/problems/1515)
[Submission](https://tioj.ck.tp.edu.tw/submissions/264483)
> 題目敘述
裸後綴數組 LCP。

這題我想好久，一直想不通轉移的條件以及k-1的原因，不過最後還是想通了！這一題只要把 $lcp$ 求出來之後，找最大值就是題目要求的答案。

#### 程式碼

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld long double
#define int long long
#define N 200005
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
        rep(i,1,n-1)
            rk[cur[i].id] = (cur[i-1]==cur[i] ? rk[cur[i-1].id]:i);
            //設定rk，與前一個相同則設定跟前一個一樣
        rep(i,0,n-1){
            cur[i] = {rk[i],rk[min(n,i+k)],i};
        }
    }
    sort(all(cur),cmp);
    rep(i,0,n-1)sa[i] = cur[i].id;
}

vector<int> LCP(string s){
    vector<int> rk(n,0),lcp(n,0);
    rep(i,0,n-1)rk[sa[i]] = i;      //利用sa反函數得到rk
    int k = 0;
    rep(i,0,n-1){
        if(k)k--;
        if(rk[i] == n-1)continue;   //rk[n-1]未定義
        int j = sa[rk[i]+1];        //下一名後綴從何開始
        while(i+k<n && j+k<n && s[i+k] == s[j+k])k++;
        lcp[rk[i]] = k;
    }
    return lcp;
}

signed main(){
    Orz;
    cin>>n;cin.ignore();
    string S;getline(cin,S);
    suffix_array(S);
    vector<int> lcp = LCP(S);
    
    int ans = 0;
    rep(i,0,n-1)ans = max(lcp[i],ans);
    cout<<ans<<endl;
}
```

## 心得

字串演算法跟最短路徑、計算幾何比起來，數學推導成分就多了很多，有許多的遞迴概念，例如失敗函數以及z函數的概念，也有很多是要理解在不同條件下做什麼事，以及其他優化處理。最初的是字典樹Trie，就是一些資料結構的運用；接下來的KMP、Z Algorithm以及LPS就很吃觀念，追蹤整個程式的過程。最後是後綴數組，在這邊卡了超久，遲遲無法理解倍增的概念、以及LCP的作法。可能這就是頓悟吧，每天看了同篇解說很久之後，總有一天會突然頓悟，想到演算法的精髓！
