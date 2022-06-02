---
title: 婚姻匹配問題（Stable Marriage Problem）
date: 2022-1-1
tags: 
    - 匹配
    - 數學
categories:
    - 數學筆記
mathjax: true
---

## 問題描述

對於婚姻匹配問題有以下描述。

> Given n men and n women, where each person has ranked all members of the opposite sex in order of preference, marry the men and women together such that there are no two people of opposite sex who would both rather have each other than their current partners.
>
> When there are no such pairs of people, the set of marriages is deemed stable.
<!--more-->
在所有婚姻狀態均「穩定」的已婚社群，每對婚姻中的男、女，各自都無法找到更心儀的、喜歡他、她甚於喜歡自己配偶的對象。換句話說，男人即使心中有他認為比太太更好的女人，這女人也不會認為他比自己的先生好。同樣地，女人心中即使有她認為比先生更好的男人，這男人也不會認為她比自己的太太好。

下圖為四男（小寫字母）以及四女（大寫字母）對異性的偏好順序，第一格代表男生對女生的偏好順序，第二格則代表女生對男生的偏好順序。

|     |  A  |  B  |  C  |  D  |
| :---: |:---:|:---:|:---:|:---:|
| a   | 1,3 | 2,3 | 3,2 | 4,3 |
| b   | 1,4 | 4,1 | 3,3 | 2,2 |
| c   | 2,2 | 1,4 | 3,4 | 4,1 |
| d   | 4,1 | 2,2 | 3,1 | 1,4 |

我們發現到只有$(a,C),(b,D),(c,A),(d,B)$是唯一一組穩定的配對，對於其他的配對必可找到一對男女喜歡彼此的程度勝過對原本的伴侶。

## Gale-Shapley Algorithm（男方求婚法）

演算法步驟如下：

```
 所有男生各自向他們最喜歡的女生求婚;
 每當（有女生被兩個以上的男生求婚）
 {    這個女生暫時保留所有求婚者中她最喜歡的一個，而拒絕其他人；
      被拒絕的男生退而求其次，向不曾拒絕過他的人當中最喜歡的女生求婚；}
```

### 數學證明

#### 小觀察

如果是男生不斷向女生求婚，則有以下的觀察：

1. 一個女生一旦訂婚，她對訂婚對象的喜好程度會不斷增加。
2. 一個男生一旦訂婚，他對訂婚對象的喜好程度會不斷下降。

#### 引理

1. Gale-Shapley Algorithm 的結果必定是**完美配對**。
{% note success %}

注意到每一個女生最多只會拒絕$n-1$個男生（必定留下一個），這時候每個女生都一定會被求婚過，否則，因為每一個女生只會暫時接受一個求婚者，一定還有一個未被接受的男生還可以向某一位女生求婚。
{% endnote %}

2. Gale-Shapley Algorithm 的結果必定是**穩定的**。
{% note success %}
假設這個匹配裡出現不穩定配對，不失一般性假設是$(a,B)$，其中 $a$ 喜歡 $B$ 勝過於他的配偶 $A$，而 $B$ 喜歡 $a$ 多過他的配偶 $b$。由於 $a$ 比較喜歡 $B$，他必定曾經向 $B$ 求婚過，但最後沒有和 $B$ 配對，表示 $a$ 被拒絕而當時 $B$ 身旁是有一個求婚者是 $B$ 比較喜歡的，因此這個求婚者在 $B$ 的排行中也優先於 $b$ （優先於 $a$ 因而優先於 $b$），根據觀察一，不可能最後 $B$ 跟 $b$ 配對在一起（女生對訂婚對象喜好程度只會不斷增加），矛盾。
{% endnote %}

#### 證明

根據引理1,2，我們知道 Gale-Shapley Algorithm 輸出的匹配必定是完美配對且不存在不穩定配對。根據定義，得證。

#### 複雜度分析

證明算法的複雜度為：$O(n^2)$。

1. 總共不會超過 $n^2$ 個求婚要求：每個男生只會對一個女生求婚最多一次，總共有 $n$ 個女生，因此最多有 $n^2$ 個求婚要求。
2. 對於每一個求婚要求以 $O(1)$ 處理：可以藉由一個二維陣列 $O(1)$ 比較女生對男生的喜好程度，同時維護一個佇列使我們能 $O(1)$ 取得男生要下一個要配對的女生。

得證 Gale-Shapley Algorithm 的複雜度是 $O(n^2)$。

### 實作

[題目連結](https://onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=3616)

這題是女生求婚法。

比較難處理的部分是輸入，其他就照著虛擬碼執行算法。在以下程式碼中，我們使用*cur_que*放入尚未成功配對的女生，每一個女生下一個要配對的男生則儲存在*que*中，並利用*Man*記錄男生目前配對到的女生。

執行完演算法之後，利用反函數找出*Man*女生配對到的男生並輸出。

```cpp=
#include <bits/stdc++.h>
#define int long long
#define pii pair<int,int>
#define ff first
#define ss second
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
const int N = 1005;
int t,n;
pii P[N][N];

vector<int> gale_shapley(queue<int> *q){
    queue<int> cur_que;
    vector<int> Man;Man.resize(n+1,0);
    for(int i = 1;i <= n;i++)cur_que.push(i);
    while(!cur_que.empty()){
        int woman = cur_que.front(),man = q[woman].front();
        q[woman].pop();
        if(!Man[man]){
            Man[man] = woman;
            cur_que.pop();
        }
        else if(P[woman][man].ss < P[Man[man]][man].ss){
            cur_que.push(Man[man]);
            Man[man] = woman;
            cur_que.pop();
        }
    }
    return Man;
}

signed main(){
    IOS;
    cin>>t;
    while(t--){
        cin>>n;
        queue<int> q[N];
        for(int i=1;i<=n;i++)for(int j=1;j<=n;j++){
            int temp;cin>>temp;
            P[i][temp].ff = j;
            q[i].push(temp);
        }
        for(int i=1;i<=n;i++)for(int j=1;j<=n;j++){
            int temp;cin>>temp;
            P[temp][i].ss = j;
        }
        int ans[N];
        vector<int> Man = gale_shapley(q);
        for(int i = 1;i <= n;i++)ans[Man[i]] = i;
        for(int i = 1;i <= n;i++)cout<<ans[i]<<endl;
        if(t)cout<<endl;
    }
}
```

## 延伸問題

1. 證明男方求婚法會給出男性的最佳匹配。
2. 如何有效率的找出所有可能的穩定匹配？
3. 承上，根據Wiki所述，試證明$n$男$n$女穩定匹配數的平均會漸進於$e^{-1}n\ln n$。
4. 證明找出穩定匹配數的問題是一個[#P-complete](https://en.wikipedia.org/wiki/%E2%99%AFP-complete)的問題。

下圖是男女對數$n$跟穩定匹配數$S$的關係圖，其增長的速度與$n\ln n$同級：
![](https://i.imgur.com/phjJi3v.png)

## 參考資料

[Wiki:Gale–Shapley Algorithm](https://en.wikipedia.org/wiki/Gale%E2%80%93Shapley_algorithm)

[演算法筆記:Matching](https://web.ntnu.edu.tw/~algo/Matching2.html)

[Michael Dzierzawa, Marie-José Oméro, Statistics of stable marriages, Physica A 287 (1–2) (2000) 321–333](https://ur.booksc.eu/book/4740030/49e7a5)
