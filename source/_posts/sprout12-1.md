---
title: "[題解]NEOJ 265 欸迪的字串"
date: 2021-1-10
tags: 
    - 資訊之芽
    - 題解
categories: 資芽題解
mathjax: true
---

### 欸迪的字串
<!--more-->
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
