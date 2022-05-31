---
title: 字串演算法 (String)
date: 2021-9-4
tags: 
    - 字串
    - 演算法
categories:
	- C++進階主題
	- 字串演算法

mathjax: true
---

這個暑假看了動態規劃(1),(2)，線段樹、最短路徑、計算幾何、字串演算法，這一篇的完成算是暑假的一個里程吧！接下來繼續學習不同的主題，再把筆記更新到部落格中！

## 課程內容

### 字串

- 定義
  - 大寫 $\Sigma$ 表示字元集
  - 字串：有限個字元集組成
  - $|\Sigma|$ 字元集大小、|S|字串長度
  - S[a:b]表示連續從字元a到b
<!--more-->
- 子字串：S[a:b]
- 前綴：S[0:b]
- 後綴：S[a:|S|-1]

### 字典樹Trie

- 定義
  - 例題：字串出現次數
  - 一顆由根、邊一綠往下指的有向樹
  - 每個邊為字元、每一點代表字串
  - 每經過一條邊，字串加上邊的字元
  - 根節點為空字串！
- 操作
  - 查詢字串：$O(L)$，由根順著邊往下找
  - 插入字串：$O(L)$，不斷往下走直到空節點，在Trie加入一個節點
  - 節點必須記錄當前是否為有效字串
  - Trie結構中包含：字串、紀錄字串出現次數

### KMP

- 功能：進行字串匹配
- 例題：字串S在字串T哪些位置出現
  - 暴力匹配：時間 $O(|S|\times|T|)$
- F[i]表示當配對成功A[0:i]後即配對失敗，將A[F[i]]對齊原本A[i]的位置
- 搭配F[i]調整在串移動的長度
- 匹配：複雜度 $O(|T|)$，建立F函數：$O(|S|)$

### 漢明距離(Hamming distance)

- 兩個等長字符串對應位置的不同字符的個數

### Z-value

- z[i] 是指由 s[i] 開始的字串，與 s[0] 開始的字串可以匹配到多長
- S[0:k-1] = S[i:i+k-1]
- z[0] = 0

### Rolling Hash

- 之前隨機講過

### 後綴數組（Suffix Array）

- 將一個字串的所有後綴進行排序
- 基數排序（Radix Sort）

### 最長共同前綴（LCP,Longest Common Prefix）

- 兩字串的最長共同前綴
- 將最長共同前綴轉換成區間最小值問題

## 字串演算法主題

### 字典樹Trie

字典樹是以指標型態建立的一棵樹，邊代表一個字元、節點代表從根一路走來的邊形成的字串，從根節點開始（根節點為空），每經過一條邊，就把字串加上那一條邊對應的字元，直到找出要匹配的字串。如果有多筆獨立的詢問，只要加上Delete函數就可以了！

#### Trie結構

實作上的陣列c指向個別的字元，不一定要用cnt，根據題目的所求調整不同的變數設定。

```cpp=
struct Trie{        //利用指標建立一棵樹
    Trie* c[26];    //對應a-z每一條邊
    int cnt;        //字串出現次數
    Trie(): cnt(0){ //初始設定
        memset(c,0,sizeof(c));
    }
};
Trie* root;
```

#### Insert函數

在插入的過程中言錄新增路徑，一樣嘢可以根據題目要求在過程中進行變數紀錄等。如**NEOJ 267 自動完成系統**。

```cpp=+
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
```

#### 查詢函數

一個字串對應到唯一的路徑，從根節點根據每一個字元決定路徑。

```cpp=+
int find(char *s){              //查找字串s
    Trie *ptr = root;           //根節點尋找
    while(*s){                  //無此字串，回傳次數0
        if(!ptr->c[ch(*s)])return 0;
        ptr = ptr->c[ch(*s)];
        s += 1;                 //字串下一個字元
    }
    return ptr->cnt;            //回傳字串出現次數
}
```

#### Delete函數

```cpp=
void clear(Trie *s){
    for(int i=0;i<26;i++){
        if(s->c[i]){
            clear(s->c[i]);
            delete s->c[i];
            s->c[i] = nullptr;    //很重要
        }
    }
}
```

### KMP Algorithm

首先要求出Failure Function，它可以在 $O(|S|)$ 建立。失敗函數的定義是：
> $F[i]$ 表示當成功配對 $A[0:i]$ 之後，配對失敗時，我們會將 $A[ F[i]]$ 對齊原本 $A[i]$ 的位置

如果寫成數學式的定義：

$$ F_A(J)=\left\{
\begin{array}{l}
-1,&&\text{if j = 0}\\
max\{p:A_{0...p} = A_{j-p...j} \text{ and 0≤p<j}\},&&\text{otherwise}\\
\end{array} \right. $$

這個函式以中文來說就是「**最大前綴後綴**」！

這個函數可以讓我們知道，當配對在較短字串 $S$ 在第 $i$ 位配對失敗時，要找到第 $F[i]$ 去繼續比對。從比較直觀的角度去理解，就是把字串要往右移幾格才能正確匹配上，如果配對失敗，則尋找更前面更短的子字串試試看。

以比較數學的角度看到底要怎麼建立Failure Function，可以從以下推導得知：

{% note info %}
假設 $F[i] = x, x≠-1$，根據上面的定義有這樣的等式：$A[0:x] = A[i-x:i]$
分成以下兩種情況做討論：

- $A[x+1] = A[i+1]$
這種情況就是兩邊的下一個字串都相同，直接將繼承前面的長度為x的子字串，因此有關係式$F[i+1] = x+1$

- $A[x+1] ≠ A[i+1]$
這種情況比較棘手，我們無法繼續使用之前長度為x的子字串，因此我們要尋找前面更短的字串進行匹配。假設我們找到 $K$ 滿足 k<x，同時 $A[0:k] = A[i-k:i]$ ，這時候我們只需確認 $A[k+1]=A[i+1]$ 是否成立即可，如果不成立則繼續尋找比 $K$ 更短的子字串。

<br>

從剛剛的關係式，我們可以繼續寫下去： $A[0:k] = A[i-k:i]=A[x-k:x]$，理由是i的後綴與x後綴相同，因此 k的後綴就會和x的後綴相同。由失敗函式的定義推斷，我們要找的 $k$ 就會是 x 的失敗函式，$K = F[x]$。

如果找到的k不滿足 $A[k+1]=A[i+1]$ ，則會繼續尋找下一個更小的 $k$ 值直到滿足條件或是 $k=-1$ 為止。
{% endnote %}

再來則是KMP MATCH，能在時間複雜度 $O(|T|)$ 內將匹配出來的位置找出來。具體的方法與建立Failure Function 相近，如果 $A[p+1]≠A[i+1]$，則尋找更小的字串長度 $k<F[i]$ 看能否繼續匹配。當匹配成功，記得把p設為F[p]，因為匹配完成其實也可以看作是下一個字元匹配失敗（$S$ 的空字元對上 $T$ 的下一個）。

至於時間複雜度的部分，分析一下while迴圈總共會執行的次數，當每執行一次，p的值一定會遞減，而p只會在每一個迴圈最多加上一，因此p的增加會是字串長度 $|T|$ 的常數倍，使總複雜度為 $O(|T|)$。

#### Build Function

```cpp=
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
```

#### Match Function

```cpp=+
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
```

### Hash (Robin-Karp Algorithm)

雜湊算法的核心概念就是以下公式，可以透過它進行字串比對等等。
$$H(s[1:n]) = \sum_{i=0}^n S_i\times C^{n-i}$$
詳細內容可以參閱這一篇：[隨機演算法](https://peienwu.com/sprout9/)

### Z Algorithm (Gusfield's Algorithm)

Z函數的定義是對於字串長度 $n$ 的字串 $S$ ，$z[i]$ 函數代表 $S[0:n-1]$ 和 $S[i:n-1]$ 的**最長共同前綴**，也就是LCP長度。定義 $z[0] = 0$。

有講義上面寫Z Function很簡單，但我覺得有夠難，難度跟KMP的理解差不多。根據Z函數的定義，我們可以$O(N^2)$建立z函數，顯然時間有些太多了。如果我們算 $Z[i]$ 的值可以用 $Z[0:i-1]$ 轉移過來，會省下許多時間，把時間降成 $O(N)$。

#### 算法概念

整個算法的核心概念就是利用前面已經算好的Z Function去推現在的值，有點像動態規劃的概念。在整個過程中我們會維護右端點最大的匹配段（與前綴匹配），其表示為 $[l:l+Z[l]]$，也可以寫作 $[l:r]$，保證 $l ≤ r$。在計算 $Z[i]$ 的過程中，分別討論以下三種情形：

1. $i ≤ r$
根據定義，因為區間 $[l:r]$ 本身是前綴，因此當 $i$ 在區間中間時會將區間分成左右兩半邊，將正個區間往前平移會讓 $i$ 對應到 $i-l$ 的位置，又此時區間為 $[0:r-l]$ ，因此$z[i] = min(z[i-l],r-i)$。

    - $z[i-l] ≤ r-i$
已經知道 $z[i-l] < r-i$ 表示在前後兩區間（分別從0,i開始）在一樣的情況下做多不超過 $z[i-l]$ 長度的匹配，因此第 $z[i-l]$ 的下一個字元必定無法繼續匹配成前綴。
    - $z[i-l] > r-i$
在這種情況下已知 $s[0:r-l] = s[l:r]$，根據定義 $s[i-l:r-l] = s[i:r]$。因為 $z[i-l] > r-i$，$z[i-l]$ 右端的範圍會超過 $r$ ，也就是說超過 $r$ 之後這個性質 $s[i-l:r-l] = s[i:r]$ 就不會成立，但可以確定 $z[i]$ 至少為 $r-i$。接著就暴力枚舉即可！

上面的內容可以用下圖解釋，兩條紅線段是一樣的，$x=i-l$ ，接著討論$Z[x]$ 的長度就可以知道該如何更新。
![](https://i.imgur.com/6WU1JWG.png)

2. $i > r$
這種情況我們可以直接暴力跟前綴匹配。

#### 程式實作1

程式碼中 $bst$ 表示的是左界 $l$，右界則是 $bst+Z[bst]$。

```cpp=
void Z_algo1(char *S,int *Z){
    int bst = 0;                            //相當於左界，大小為z[bst]
    Z[0] = 0;
    for(int i=1;S[i];i++){
        if(Z[bst]+bst < i)Z[i] = 0;         //直接暴力枚舉
        else Z[i] = min(Z[i-bst],bst+Z[bst]-i);
        while(S[Z[i]]==S[i+Z[i]])Z[i]++;    //依序暴力枚舉
        if(Z[i]+i > bst+Z[bst])bst = i;     //更新更遠的右界
    }
}
```

#### 程式實作2

這個實作超級短，沒有幾行就解決了，但他的效果卻是一樣的！

```cpp=
void Z_algo2(char *S,int *Z){
    int l = 0, r = 0;                       //左右界
    Z[0] = 0;
    for(int i=1;S[i];i++){
        Z[i] = max(min(Z[i-l],r-i),0);
        while(S[i+Z[i]] && S[Z[i]] == S[i+Z[i]]){
            r = i+Z[i];               //更新右界
            Z[i]++;
        }//保證當Z[i]從Z[i-l]轉移時不會被更新！
    }
}
```

#### 時間複雜度

根據以上兩個程式碼可以發現，右界 $r$ 或是 $bst+Z[bst]$ 在過程中是不斷增大的，且r必定不會超過n，因此迴圈跑下來複雜度會是 $O(N)$。

### LPS (Manacher's Algorithm)

LPS (Longest Palindromic Substring)就是最長回文子字串，Naive的作法是每次 $O(n)$ 向兩側擴展，時間是 $O(n^2)$。

Manacher's Algorithm這個演算法的概念和Z Alogorithm相近，可以在 $O(n)$ 的時間內找出以每一個點為中心之最長回文長度。回文的定義是無論從正序或是逆序看一個字串都是一樣的，分為兩種：一種是奇數的對稱，也就是以一個字元為對稱中心往兩側擴展；另一種則是以字元間的空格為對稱中心向兩側對稱。

這兩個的性質很不同，在處理的時候我們也不知道到底是呈現怎麼樣對稱的狀況，因此可以使用一種手法：將每一個字元中間插入同樣沒有出現過的字元，如此一來不論是偶數或是奇數長度的字串，在加入這個沒有出現過的字元之後，都會變成奇數長度的回文了！

定義 $z[i]$ 為以 $s[i]$ 為中心，最長的回文長度LPS（如果字元i是自己回文，定義Z[i] = 0]）。以字串abba來說就是以下情況：

| 編號   | 1   | 2   | 3   | 4   | 5   | 6   | 7   | 8   | 9   |
| ------ | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 原字串 | a | b | b | a  |   |     |     | ||
| 變更後| . | a  | . | b | . | b | . | a | .
| Z函數 | 1 | 2 | 1 | 2 | 5 | 2 | 1 | 2 | 1 |

跟Z Alogrithm 一樣，維護一個右界最遠的區間 $[l,r]$，作為在算 $z[i]$ 時能使用到的 $z[0:i-1]$ 的最大值，令他為 $r$。取j滿足 $j+z[j]$ 有最大值，分成以下兩個條件做討論：

**1. r < i**
這種情況就表示不能用之前算的東西去更新現在的值，因此只能暴力枚舉 $s[i]$ 的左右兩側，看最長回文的長度為何。

**2. r > i**
分成三種情況討論（比z algorithm多了一個），首先因為 $r > i$，右界覆蓋了 $i$，表示我們可以從 $j$ 的另外一端映射出與 $s[i]$ 相同的 $s[i']$（以 $j$ 為中心回文呈現兩側對稱），映射的索引值為 $2j-i$，如下圖：

![](https://i.imgur.com/Pf1S21i.png)

因為 $z[i']$ 已經計算過了，因此可以將 $r-i$ 以及 $z[i']$ 的大小分成三種情況討論：

- $z[i'] < r-i$
這種情況表示 $z[i']$ 無法繼續往右邊更新，因為 $i$ 與 $i'$ 都是在以 $j$ 為中心的回文中，兩邊是一樣的，代表 $z[i] = z[i']$ 且不可能再被更新成更大的範圍。

- $z[i'] = r-i$
這種情況下是要枚舉的，從兩側映射的關係知道 $z[i]$ 的長度至少為 $r-i-1$，因此將 $z[i]$ 設為 $r-i$ 繼續枚舉就可以了！

- $z[i'] > r-i$
由下圖觀察發現，$z[i]$ 不可能比 $r-i$ 還要大，直接將值設為 $r-i$ 即可。
![](https://i.imgur.com/zsb6blx.png)

#### 時間複雜度

觀察到while迴圈執行的狀況，只有當 $r < i$ 以及 $z[i'] = r-i$ 時右界才有被更新的可能，兩種情況都會讓右界 $r$ 遞增，範圍最大到 $n$ ，因為不會減小的關係，總時間複雜度為線性的 $O(n)$！

#### 程式碼

```cpp=
void Longest(){
    n = strlen(T);m = 2*n+1;
    memset(S,'.',m);
    for(int i=0;i<n;i++)S[2*i+1] = T[i];    //間隔插入沒出現過字元
    Z[0] = 1;                               //計算時使用右開區間
    int l = 0,r = 0;                        //維護最大右界左右界
    for(int i=1;i<m;i++){
        Z[i] = max(min(Z[2*l-i],r-i),1);
        while(i-Z[i]>=0 && i+Z[i] < m && S[i+Z[i]]==S[i-Z[i]]){
            l = i;r = i+Z[i];
            Z[i]++;
        }
    }
}
```

### 後綴數組

一些基本定義，我們定義 $suf[i]$ 是字串 $S$ 從 $i$ 開始的後綴，也就是 $S[i:n-1]$。將一個字串所有的後綴取出來按照字典序進行排序，就是**後綴數組**。總共有三個複雜度：$O(n^2\log n)$、$O(n\log^2 n)$、$O(n\log n)$ 分別是使用暴力、倍增、基數排序的優化。

#### 暴力解 $O(N^2\log n)$

$O(n)$ 的字串比對，排序 $O(n\log n)$，因此時間複雜度為 $O(n^2\log n)$。

```cpp=
int n,m;
char S[10] = "algorithm";

bool cmp(int a,int b){
    return strcmp(S+a,S+b) < 0;
}

int main(){
    int SA[10];
    n = strlen(S);
    rep(i,0,n-1)SA[i] = i;
    sort(SA,SA+n,cmp);
    rep(i,0,n-1)cout<<SA[i]<<" ";
    cout<<endl;
}
```

#### 倍增+Quick Sort

使用倍增可以讓複雜度做到 $O(n\log^2 n)$，其原理主要是讓比較的時間併入排序，同時多做 $O(\log n)$ 層的排序。定義 $sa[i]$ 為將後綴排序後第 $i$ 小的後綴編號；$rk[i]$ 表示後綴 $i$ 的排名。

倍增顧名思義，跟將數量乘上兩倍有關，因此會帶一個log。下圖就是一個排序的示例，首先按這字典序初始每一個字元的排名 $rk[i]$，進行 $O(\log n)$ 層，每一層用 $O(n\log n)$ 的時間進行排序。倍增讓原本的字串比較 $O(n)$ 降到 $O(\log n)$ 。

接下來的每一層的每一個字元 $s[i]$，在比較大小的時候，將 $rk[i]$ 以及 $rk[i+k]$ 組成一個 $pair$ 進行排序，從第一層開始，每一層的 $k$ 都會增加為2倍（倍增的概念）。pair的first就好比倍增時前 $s[i:i+k]$ 的排名、second就是 $s[i+k+1:i+2k]$ 的排名，搭配字典序是按照由前到後進行排名，比完fisrt才會比second，如此一來我們可以利用倍增的性質，也就是已經排名好的較短長度的字串，直接利用排好的名次進行下一輪的排序，這就導致我們不需要對每一個字元都看過一遍！

![](https://i.imgur.com/l1aGK8f.png)
[圖片出處](https://oi-wiki.org/string/sa/)圖中的黑線岔開距離都會因為每一層而越岔越開（乘上2倍），也就是「倍增」所代表的意義！

```cpp=
void suffix_array(string S){
    n = S.size();
    int lg = 32-__builtin_clz(n);//回傳第一個1之前0的個數（二進位）
    vector<pt> cur(n,{0,0,0});
    vector<int> rk(n+1,-1);
    rep(i,0,n-1)cur[i] = {S[i],0,i};
    rep(p,0,lg){                //進行O(lgn)次
        int k = 1 << p;         //現在倍增的大小
        sort(all(cur),cmp);
        rk[cur[0].id] = 0;
        rep(i,1,n-1)            //設定rk，與前一個相同則設定跟前一個一樣
            rk[cur[i].id] = (cur[i-1]==cur[i] ? rk[cur[i-1].id]:i);
        rep(i,0,n-1)            //倍增pair的second
            cur[i] = {rk[i],rk[min(n,i+k)],i};
    }
    sort(all(cur),cmp);
    rep(i,0,n-1)sa[i] = cur[i].id;
}
```

#### 搭配 Radix Sort

基數排序 (Raddix sort) 以及計數排序 (Counting sort) 兩者是有點從屬的關係，計數排序是基數排序的子程序，也就是說基數排序是透過對每一位進行計數排序完成的！

首先，我們需要 $O(n)$ 的空間進行每一輪的計數排序，對每一個元素進行排名（從1到n-1），如此一來才能確保不會有溢位的問題。先對第二個關鍵字進行排序，排完之後再按照第一個關鍵字排序。Raddix Sort 是穩定的，相同元素為在前面在排序之後一定會在前面。

有幾個地方有實作上進行基數排序的限制，首先第一輪會按照個別字母的字典序進行排序，用快排等基於比較的排序方法可以順利完成，但可惜基數排序不行，一定要乖乖的按照 1 到 n-1 順序進行排名。因此在第一輪會先使用 $std::sort$ 排名，之後進行raddix_sort！

最後一個小小的地方，就是當第二個 key 的值為 -1的情況（倍增超出範圍），在比較時可以直接push進去答案裡面，因為並沒有一個box的index 是-1，同時他們也是最小，丟進去即可。

**時間複雜度：** 每一層進行 $O(n)$ 排序，因為倍增共有 $O(\log n)$ 層，因此總時間複雜度為 $O(n)\times O(\log n) = O(n\log n)$！

```cpp=
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
```

### 最長共同前綴 LCP (Kasai's Algorithm)

最長共同前綴，以現在字串演算法中，假設 $lcp(i,j)$ 為後綴 $i$ 與 $j$ 的最長共同前綴。在[這篇文章](https://www.geeksforgeeks.org/%C2%AD%C2%ADkasais-algorithm-for-construction-of-lcp-array-from-suffix-array/)中給出這個定義：
> A value lcp[i] indicates length of the longest common prefix of the suffixes inexed by suffix[i] and suffix[i+1]

定義 $lcp[i]$ 為後綴 $i$ 與後綴 $i+1$ 的最長共同前綴，其中 $lcp[n-1]$ 沒有定義。再複習一下，$rk[i]$ 為第 $i$ 個後綴數組的排名$rk[i]$、$sa[i]$ 為排名為 $i$ 的字串所對應到的從 $sa[i]$ 開始的後綴數組。

到底我們要求的LCP是什麼？如果只是給兩個字串要求出共同最長前綴，那我們只要 $O(min(L_A,L_B))$ 就好，是可以接受的複雜度。但Kasai's Algorithm不是要求這個。一般都在講到後綴字串之後講到LCP，為的就是要求出所有的後綴數組中，任兩個字串的共同最長前綴為何。以一個字串長度為 $l$ 來說，後綴的配對數量共有 $O(l^2)$ 組，暴力肯定會TLE。

求出 $lcs$ 需要有兩個引理：

- **第一個引理**
$$lcp[rk[i]] ≥ lcp[rk[i-1]]-1$$
其中 $i$ 為**尚未經過排序**的從 $i$ 開始的後綴字串。

**說明：** 將後綴數組排序之後有點像將相似的字串排在一起（按字典序由小排到大），鄰近的字串其實代表著一個意義，兩者相似程度越高。因此 $lcp[rk[i-1]]$ 某種意義代表著與 $i-1$ 開始的後綴數組（簡稱後綴 $i-1$）跟其他字串最大的共同前綴長度。

接著我們看 $lcp[rk[i]]$ ，後綴 $i$ 就是後綴 $i-1$ 刪掉一個前綴字元的結果。我們假設跟後綴 $i-1$ 相近的那個字串叫做 $T$ （其實就是後綴 $rk[i-1]+1$），把 $T$ 刪除一個前綴字元之後所形成的字串（也就是比 $T$ 再短一字元的後綴）也就可以跟後綴 $i$ 進行匹配，其長度因為被刪掉一個前綴字元所以少一，因此得到了以上式子。

聽說很難證明，不過至少可以情感上的接受這件事是對的！

- **第二個引理**
$$lcp(S_i,S-j) = min_{i≤k<j}(S_k,S_{k+1})$$

為什麼可以算出最近的排名的LCP即可得到最長的LCP？從這個定理可以看出來，對於後綴 $i$ 以及後綴 $j$ ，取 $min$ 的原因是要確保所有的中間後綴 $k$ 都包含了下界 $k$ ，也就是說每個在 $i$ 與 $j$ 中間的人都至少跟前一個人相同個字元。

---
以下以字串 $aabaabc$ 作為範例：

| 後綴 | RK  | String  |
| ---- | --- |:------- |
| 0    | 0   | aabaabc |
| 1    | 2   | abaabc  |
| 2    | 4   | baabc   |
| 3    | 1   | aabc    |
| 4    | 3   | abc     |
| 5    | 5   | bc      |
| 6    | 6   | c       |

以下是SA與對應的String

| RK  | SA  | String  | LCP |
| --- | --- | ------- | --- |
| 0   | 0   | aabaabc | 3   |
| 1   | 3   | aabc    | 1   |
| 2   | 1   | abaabc  | 2   |
| 3   | 4   | abc     | 0   |
| 4   | 2   | baabc   | 1   |
| 5   | 5   | bc      | 0   |
| 6   | 6   | c       | 0   |

以下是找LCP流程：

- 首先從後綴0開始，找到對應的下一個rk，也就是後綴3，得到LCP為3
- 接下來看後綴1，查表得到RK為2，找到RK為3的找LCP，得到2
- 看後綴2，與RK為4,5名找LCA，得到1
- 後綴3，看RK 1,2，LCA為1
- 接下來以此推累

接下來有兩種不同設定方法，端看要採用的是上表還是下表的字串排序方法。我以上表為例，程式執行時印出順序：$3211000$；也可以採用後綴數組排序後的結果（一般都採用此方法），直接對應出順序（對照的是下表）：$3120100$。

```cpp=
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
```

**時間複雜度：** 主要觀察 $k$ 的變化，$k$ 最多就是n，最少是0，最差的情況是 $k$ 被減掉 $n$ 次，加上 $2n$ 次，因此複雜度是線性 $O(n)$！

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
