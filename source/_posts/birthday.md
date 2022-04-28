---
title: 生日問題（Birthday Paradox）
date: 2021-7-5
tags: 
    - 數學
    - 2021暑期筆記
    - 機率
categories: 
    - 數學筆記
mathjax: true
---

有同學問我生日問題的機率感覺蠻好玩的，決定好好研究一番。生日悖論其實不是一個悖論，只是有點違背直覺而已，並非數學中定義的悖論！這一篇會用蒙地卡羅模擬來進行與理論的比較

## 問題敘述

題目：一個房間要多少人，則兩個人的生日相同的機率要大於50%？

答案是<font color="#f00">23</font>人。
<!-- more -->
如果想要看1到100人有同一天的機率可以[點這裡！](https://gist.github.com/peienwu/bd6edf34416f352ba97b94c67d5acb52)

## 機率與人數的關係

### 兩種理解方式

對於生日問題可能會有兩種理解方式：

1. 題意：<font color="#f00">「23人之中兩兩之間存在生日相同」</font>
    對於原本題目要闡述的意義可以列出以下數學式：
    $$\begin{split}p(n)&=1-p'(n) \\&= 1-(1\times\frac{364}{365}\times\frac{363}{365}\times\cdots\frac{365-(n-1)}{365})\end{split}$$
2. 錯誤理解：<font color="#f00">「其他22人與你的生日相同的機率」</font>
    這樣的理解會造成算出來的機率大為減少（用全部扣掉皆不相同）：
    $$q(n) = 1-(\frac{364}{365})^n$$

有了以上兩個關係式，就可以進行圖表的繪製。x軸表示人數，y軸表示機率，可以看出來兩種理解方式會造成機率有很大的差別！
![](https://i.imgur.com/NcIRKE7.png)

上圖繪製程式碼：

```python=
import numpy as np
import matplotlib.pyplot as plt

plt.title("Possibility to Number of people", fontsize=20) #圖表標題
plt.xlabel("Number of people", fontsize=16) #x軸標題
plt.ylabel("Possibility", fontsize=16) #y軸標題

d = 1
p = 1
x = []
y1 = []
y2 = []
for i in range(1,365):
    y1.append(1-p)
    p = p*d
    d = d-(1/365)

for i in range(365,400):
    y1.append(1)

d2 = 364/365
for i in range(1,400):
    x.append(i)
    y2.append(1-d2)
    d2 = d2*(364/365)

plt.plot(x,y1,'red',label='p(n)')
plt.plot(x,y2,'blue',label='q(n)')

plt.legend(loc = 'upper left')
plt.show() #顯示繪製的圖形
```

這張圖表還可以告訴我們，任兩個人生日相同的機率很高，但相對的，即使有400個人，要有人跟你的生日相同的機率比6成高一點而已，告訴我們「全部的日期至少有一人生日」的機率其實不高！

### 其他人與你的生日相同的機率

如果在看更仔細一點，對於「其他人與你的生日相同的機率」作圖會呈現下方的圖形
![](https://i.imgur.com/DHu0F0g.png)

因為生日是隨機的，因此在很大量數據測試下，我們可以期望共有365人的時候會出現第一個與自己生日相同日期的人！實際以亂數模擬，所得到的期望值次數與理論是相符的！
![](https://i.imgur.com/UdjoCdI.png)

```cpp=
#include <bits/stdc++.h>
using namespace std;
bool birthday[400];

int main(){
    srand((unsigned)time(NULL));
    int sum = 0,t = 5000000,times = t;

    while(times--){
        memset(birthday,0,sizeof(birthday));
        int target = rand()%365,ind=0;
        for(int i=0;;i++){
            ind = rand()%365;
            if(target==ind){
                sum+=i+1;
                break;
            }
            else birthday[ind] = 1;
        }
    }
    cout<<fixed<<setprecision(4);
    cout<<"平均於第："<<(double)sum/t<<"次"<<endl<<"會出現第一個與自己生日相同的人"<<endl;
}
```

## 至少k個人生日相同機率

對於至少k人生日相同的機率要大於50%，需要的人數如下表：
![](https://i.imgur.com/Nwpe6aH.jpg)
用程式驗證看看：

| k   | 共N人 | 機率     |
|:--- |:----- |:-------- |
| 2   | 23    | 0.506949 |
| 3   | 88    | 0.511169 |
| 4   | 187   | 0.502883 |
| 5   | 313   | 0.501057 |
| 6   | 460   | 0.502686 |
| 7   | 623   | 0.503298 |
| 8   | 798   | 0.500304 |
| 9   | 985   | 0.501191 |
| 10  | 1181  | 0.500178 |

根據[這一篇](https://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.370.8298&rep=rep1&type=pdf)提供的公式，在$k≤20$的情況下$n\cong47(k-1.5)^{\frac{3}{2}}$，這是這篇作者把k還是很小的時候進行近似，但實際的公式我還不是很了解:cry:

```cpp=
#include <bits/stdc++.h>
using namespace std;
int birthday[400],power[1000];

int main(){
    srand((unsigned)time(NULL));
    int sum = 0,t = 10000000,times = t;
    int arr[10] = {23,88,187,313,460,623,798,985,1181};

    for(int j=0;j<9;j++){
        times = t;sum = 0;
        while(times--){
            memset(birthday,0,sizeof(birthday));
            for(int i=0;i<arr[j];i++){
                int ind = rand()%365;
                birthday[ind]+=1;
                if(birthday[ind]>=j+2){
                    sum+=1;
                    break;
                }
            }
        }
        cout<<fixed<<setprecision(7);
        cout<<(double)sum/t<<endl;
    }
}
```

## 機率證明

### 公式推討

生日問題可以理解成：<font color="#f00">至少兩人生日相同的機率</font> 這個問題，而否定這個問題即為：「沒有人生日相同的機率」。因為這兩個事件的聯集即為樣本空間，可以用扣的方式得到答案！

對於房間裡有n人的情況，定義 $p(n)$ 為「至少兩人生日相同的機率」、$p'(n)$ 為「沒有人生日相同的機率」，在不考慮特殊強況（閏年等），並假設生日會平均分佈的狀況下：
$$\begin{split}p(n)&=1-p'(n) \\&= 1-(1\times\frac{364}{365}\times\frac{363}{365}\times\cdots\frac{365-(n-1)}{365})\\\\&=1-\frac{365!}{365^n\cdot(365-n)!}\end{split}$$
簡單解釋一下，對於每一個加入房間的人都有$365$種可能，因此分母皆為$365$；對於第 $i$ 個加入的人要避開前 $i-1$ 個人的生日，因此分子為$365-(i-1)$。經過整理可以得到這個有階乘又有次方的很難看的一個公式！

這時候我們可以引入泰勒公式：
$$\begin{split}e^x&=\sum_{k=0}^\infty\frac{x^k}{k!}=1+\frac{x^1}{1!}+\frac{x^2}{2!}+\frac{x^3}{3!}\cdots\end{split}$$
為什麼要引入這個公式？是因為我們想要構造出上面機率計算中的每一項 $1-\frac{x}{365}$ ，因為泰勒公式是一個無窮級數，我們可以適度的做一些取捨，例如只取第一項與第二項（在下去都是小數點4,5位以上了)，得到以下：
$$e^x\cong1+x\to e^{-\frac{x}{365}}\cong1-\frac{x}{365}$$
接著就可以把每一項替換成多項式的型態：
$$\begin{split}p'(n)&=1\times\frac{364}{365}\times\frac{363}{365}\times\cdots\frac{365-n+1}{365}\\&\cong1\times e^{-\frac{1}{365}}\times e^{-\frac{2}{365}}\times\cdots\times e^{-\frac{n-1}{365}}\\&=e^{-\frac{1+2+\cdots+(n-1)}{365}}\\&=e^{-\frac{n(n-1)/2}{365}}\\&=e^{-\frac{n^2-n}{730}}\end{split}$$

接著就可以算出正確的機率了！
$$\begin{split}p(n) &= 1-p'(n)\\&\cong1-e^{-\frac{n^2-n}{730}}\end{split}$$

### 實際機率

我利用程式實際運算求出機率，並跟公式解做比較如下：
![](https://i.imgur.com/NEJMnOa.png)
發現到誤差會隨著人數增加而有變大的趨勢，不過都是在小數點後三位的事情，誤差不到1%，所以公式解其實是可以用的！其實可以觀察到一個現象，對於$p'(n)$的理論解都會比實際值高，因為多加了幾項被我們省略掉的數字，因此計算出來的公式解會比實際機率低一些！

## 生日攻擊

生日攻擊就是利用生日問題的特性在 $\sqrt{H}$ 的時間暴力破解找出碰撞。[Google破解SHA1實現碰撞攻擊](https://www.thenewslens.com/article/62378)，如果有人可以讓兩個不同的檔案得出相同雜湊值，讓攻擊者可能偷偷把惡意的程式碼放進檔案，但得出來的雜湊值跟原本的檔案一樣，使人在沒有防備的情況下誤以為危險檔案安全，這可以達到生日攻擊（也就是找到碰撞）

### 雜湊演算法

最近有學雜湊相關東西，那剛好生日問題其實跟雜湊很有關係，因為生日可以被當作雜雜湊空間大小，空間越大雖然消耗記憶體較大但發生碰撞的機會會越小。換作是雜湊演算法中，我們想要討論的就是開的空間大小與發生碰撞的嘗試次數的關係。

首先計算生日問題人數的期望值，也就是在加入第幾個人之後，會發生有兩人生日同一天的情況，以下為模擬的情形：
![](https://i.imgur.com/m6Wiwdh.png)
透過公式的計算，可以得到不同人數對應到的機率，假設共i人的情況下機率為f(i)，則f(i)-f(i-1)為加入第i人時恰好有人生日相同的機率，就可以根據期望值的公式算出期望在共有幾人時發生碰撞。以下是計算結果：
![](https://i.imgur.com/mnyAbWk.png)
兩者的誤差極小，可以推論出在平均約在加入第24.617個人的時候會發生碰撞！

我們已經計算出對於n人的情況下任兩人生日相同的機率，這時候可以推廣到不只是365天，也就代表在雜湊空間大小為d的時候發生碰撞的機率如下：
$$\begin{split}p(n) &= 1-p'(n)\\p(n,d)&\cong1-e^{-\frac{n^2-n}{2d}}\end{split}$$

因此我寫了一個實際模擬的程式跟這個公式模擬的結果做比較，根據空間大小分別為365與1000做討論，結果如下：
![](https://i.imgur.com/3nuhPHI.png)
![](https://i.imgur.com/fKVI4QB.png)

由模擬的結果可以看出，若一年有1000天（假設而已！）則在38個人的團體中任兩人生日同天的機率已經超過50%，跟直覺相差挺大的！

### 給定機率預測最多數量

在上面的做法是人數計算機率，可以換一個方式，給定碰撞機率求最多的人數為多少。可以從上面的公式來推，以下n,H分別代表數量與空間大小：
$$\begin{split}p(n,H)&\cong1-e^{-\frac{n^2-n}{2H}}\cong1-e^{-\frac{n^2}{2H}}\\&\to e^{-\frac{n^2}{2H}}=1-p\\&\to e^{\frac{n^2}{2H}}\frac{1}{1-p}\\&\to\frac{n^2}{2H}=\ln\frac{1}{1-p}\\&\to n(p,H)\cong \sqrt{2H\ln\frac{1}{1-p}}\end{split}$$

因為我們把$n^2-n$當成$n^2$，所以在小範圍估計的時候會有比較大的誤差，不過當n很大的時候，量級就會是$n^2$，因此可以忽略一次方的$n$

### 首次碰撞的期望值次數

在上面有做一次期望值的估計，不過過程蠻麻煩的，要先算出每一個數量機率的差，再乘上數量並加總。這邊有一個公式是提供在範圍很大時的一個估計公式：

$$Q(H)\cong\sqrt{\frac{\pi}{2}H}$$

這導出一個重要的結論：對於n位密碼共有$2^n$種可能組合，確僅僅需要期望$2^{\frac{n}{2}}$次嘗試就可以遇到碰撞！以下嘗試H=800000筆與H=1500000筆數據，會有一點誤差，可能的原因是測試的樣本數不夠(100萬次)

![](https://i.imgur.com/z5Z0cvr.png)
![](https://i.imgur.com/Xh0yowN.png)

### 程式碼

實作的概念就是開一個$O(n)$的陣列紀錄，如果遇到之前出現過的就記錄下來

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
bool hash_map[2000006];

int main(){
    ios;
    srand((unsigned)time(NULL));
    int N,M;cin>>N>>M; //N筆輸入,空間大小M
    int t = 1000000;
    int times = t,sum = 0;

    while(times--){
        memset(hash_map,0,sizeof(hash_map));
        for(int i=0;i<N;i++){
            int temp = rand()%M;
            if(hash_map[temp]==0)hash_map[temp] = 1;
            else{
                sum+=i;
                break;
            }
        }
    }
    cout<<fixed<<setprecision(6);
    cout<<"對於輸入筆數共有："<<N<<"、空間大小為："<<M<<endl<<"第一次發生碰撞次數平均值為："<<(double)sum/t<<endl;
    cout<<"理論預測次數："<<sqrt(M_PI_2*M)<<endl;
    // cout<<"理論預測："<<1-exp(-(N*(N-1)/(double)(2*M)))<<endl;
}
```

## Cheryl's birthday

這一題跟生日問題沒啥關係，但既然都提到「生日」，就來看一題有趣的
> 艾伯特和柏納剛認識雪莉兒，想要知道雪莉兒的生日，雪莉兒列出了十個可能的日期：
> 5月15日、5月16日、5月19日、6月17日
> 6月18日、7月14日、7月16日、8月14日
> 8月15日、8月17日
接著雪莉兒分別告訴艾伯特及柏納她生日的月及日，以下是艾伯特和柏納的回應
>
> 艾伯特：我不知道雪莉兒的生日是哪一天，但我知道柏納也不知道
> 柏納：一開始我不知道雪莉兒的生日，但現在我知道了
> 艾伯特：那我也知道雪莉兒的生日了
> 請問雪莉兒的生日是那一天？

> [註：艾伯特的第一句話他確定柏納100%不知道生日是哪一天]

解答點此：
![](<https://i.imgur.com/FxwNYkb.png> =400x)

1. 第一句話中，柏納若要知道明確的生日日期，唯一的可能是生日日期的日在十個可能日期中只出現過一次，也就是18日和19日。但艾伯特說<font color="#f00">他知道柏納也不知道生日是哪一天</font>，因此可以可以排除5月和6月的所有日期（如果是5月或6月有一定的機會艾伯特無法確定柏納不知道是哪一天）

2. 根據第一句話柏納可以推測月份是7月或8月，而他已經知道生日是哪一天，表示他知道的日是在7月或8月中只出現過一次的日，因此可以排除7月及8月可能生日中都有出現的14日，柏納知道的日可能是15日、16日或17日。

3. 目前還有可能的生日是7月16日、8月15日及8月17日，而艾伯特在聽完第二句話就可以知道生日是哪一天，表示他知道的月份在7月16日、8月15日及8月17日中只出現一次。因此他知道的月份是7月，生日是7月16日。

<font color="#f00">答案是7月16日。</font>

這一題跟生日問題其實沒什麼關係，就當作是一個「生日」有關的有趣邏輯推理的題目吧！

## 結論

整理以上提到的公式吧

### 已知人數推算機率

$$\begin{split}p(n)&=1-\frac{365!}{365^n\cdot(365-n)!}\end{split}$$
$$\begin{split}p(n) \cong1-e^{-\frac{n^2-n}{730}}\end{split}$$

### 已知機率預測數量（也就是人數）

$$\begin{split}n(p,H)\cong \sqrt{2H\ln\frac{1}{1-p}}\end{split}$$

### 首次碰撞的期望值

$$Q(H)\cong\sqrt{\frac{\pi}{2}H}$$

沒想到生日問題可以衍伸出如此多、如此繁雜的數學公式，不僅僅是數學領域，在資安上面也扮演了一個非常重要的角色，也就是雜湊空間為H的時，根據公式我們可以期望在$\sqrt{H}$的嘗試內找到碰撞，也就是所謂的Birthday Attack！

## 附錄

一些數學證明：

![](<https://i.imgur.com/eKXfAI7.jpg> =400x)

### 參考資料

[密碼學系列之：生日攻擊](https://iter01.com/603052.html)
[維基百科：生日問題](https://zh.wikipedia.org/wiki/%E7%94%9F%E6%97%A5%E5%95%8F%E9%A1%8C)
[Diaconis and Mosteller 1989 - methods for studying coincidences](https://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.370.8298&rep=rep1&type=pdf)
