---
title: 歐拉函數 (Euler's φ Function)
date: 2021-10-10
tags: 
    - 數學
    - 高二上筆記
categories: 數學筆記
mathjax: true
---

多元選修課提到歐拉函數 $\phi$，想說蠻有趣的可以跟程式結合一下，看會發生什麼有趣的事情！

## 定義與公式

對於一個正整數 $n$，歐拉函數 $\phi(n)$ 表示閉區間 $[1,n]$ 中的正整數與 $n$ 互質的個數。定義序列 $p$ 為所有正整數 $n$ 質因數分解之後的數字。在不失一般性的假設序列 $p$ 的長度為 $r$，以質因數分解表示 $n$ 即為 $n = p_1^{k_1}\cdot p_2^{k_2}\cdots p_r^{k_r}$，則歐拉函數滿足以下關係式：
$$\begin{split}\phi(n) &= n(1-\frac{1}{p_1})(1-\frac{1}{p_2})\cdots(1-\frac{1}{p_r})
\\&=n\prod_{p|n}(1-\frac{1}{p})
\end{split}$$

<!--more-->

特別定義 $\phi(1) = 1$

來看看實際的例子，以20為例。以質因數分解來看，$20 = 2^2\times 5$，因此序列 $p = \{2,5\}$，則歐拉函數 $\phi(20) = 20\times (1-\frac{1}{2})(1-\frac{1}{5}) = 8$，稍微驗證一下，從1到20的正整數中，與20互質的有 $1,3,7,9,11,13,17,19$ 一共8個數字。

## 推導與證明

### 證明1

證明一個公式，可以先多加上一些限制，從簡單推導到較複雜的情況。

1. $n$ 是質數
這種情況比較好處理，因為所有小於等於 $n$ 的正整數都會和 $n$ 互質，因此對於 $n$ 是質數的情況下：
$$\phi(n) = n-1$$

2. $n$ 是合數
首先看簡化的情況。假設 $n = p^a$，其中 $p$ 為質數，$a$ 為一大於等於2的正整數。因為 $p$ 是質數，所有不和 $p$ 互質的數字大概會是以下這些：$p,2p,3p,...,(p^{a-1})p$，一共有 $p^{a-1}$ 個數字。我們只要把所有的數字扣掉這些即為歐拉函數 $\phi$ 的函數值：
$$\phi(p^a) = p^a - p^{a-1} = p^a(1-\frac{1}{p})$$

由這個式子推廣到歐拉函數原本的公式。我們讓 $n = p_1^{k_1}\cdot p_2^{k_2}\cdots p_r^{k_r}$，因為歐拉函數為一[積性函數](https://zh.wikipedia.org/wiki/%E7%A9%8D%E6%80%A7%E5%87%BD%E6%95%B8)，滿足 $\phi(nm) = \phi(n)\phi(m)$，其證明如下：

觀察一個有 $m$ 列 $n$ 行的矩陣，一共有 $mn$ 個數字，從 $1$ 到 $mn$ 。如以下矩陣：

$$\begin{matrix}
1 & 2 & \cdots & n
\\n+1 & n+2 & \cdots & 2n
\\\vdots&\vdots&\ddots&\vdots
\\(m-1)n+1 &  & \cdots & mn
\end{matrix}\quad$$

因為每一列都是加上不定數量的 $n$，我們從第一列挑出 $\phi(n)$ 個與 $n$ 互質的數，其他每一列也都是如此，把他們設為 $t_1,t_2,...,t_{\phi(n)}$：

$$\begin{matrix}
t_1 & t_2 & \cdots & t_{\phi(n)}
\\n+t_1 & n+t_2 & \cdots & n+t_{\phi(n)}
\\\vdots&\vdots&\ddots&\vdots
\\(m-1)n+t_1 &  & \cdots & (m-1)n+t_{\phi(n)}
\end{matrix}\quad$$

觀察一下每一直行，由於 $n$ 和 $m$ 互質，同一直行中的每一個數字和 $m$ 都會不同餘。在不同餘的情況下，每一直行都會有 $\phi(m)$ 個與 $m$ 互質的數字。因此，從上面的矩陣可以發現總共有 $\phi(n)\phi(m)$ 個與 $nm$ 互質的數字，因此有$\phi(nm)=\phi(n)\phi(m)$。

有了上述性質之後，我們就可以直接推導公式：

$$\begin{split}\phi(n) &=\phi(p_1^{k_1})\cdot\phi(p_2^{k_2})\cdots\phi(p_r^{k_r})
\\&=p_1^{k_1}(1-\frac{1}{p_1})\cdot p_2^{k_2}(1-\frac{1}{p_2})\cdots p_r^{k_r}(1-\frac{1}{p_r})
\\&=p_1^{k_1}\cdot p_2^{k_2}\cdots p_r^{k_r}\cdot(1-\frac{1}{p_1})\cdot(1-\frac{1}{p_2})\cdots(1-\frac{1}{p_r})
\\&= n(1-\frac{1}{p_1})(1-\frac{1}{p_2})\cdots(1-\frac{1}{p_r})\end{split}$$

以上就是歐拉函數公式的推導過程！

### 證明2

使用到的是排容原理。這個證明就沒有像上面那麽繁雜，不過有點難理解就是了。首先我們討論 $n$ 的質因數分解由三個質數組成，寫成：$n = p_1^{k_1}\cdot p_2^{k_2}\cdot p_3^{k_3}$，假設在小於等於 $n$ 中能被 $p_1$,$p_2$,$p_3$ 給整除的數字集合為 $A_1,A_2,A_3$，則我們要求的就是：

$$(A_1' \cap A_2' \cap A_3') = (A_1 \cup A_2 \cup A_3)'$$

我們要求的就是聯集的補集的部分：
![](https://i.imgur.com/iEWH5Na.png)

這是公式展開的情況：

$$\begin{split}\phi(n) &= n(1-\frac{1}{p_1})(1-\frac{1}{p_2})(1-\frac{1}{p_3})
\\&=n(1-\frac{1}{p_1}-\frac{1}{p_2}-\frac{1}{p_3}+\frac{1}{p_1p_2}+\frac{1}{p_1p_3}+\frac{1}{p_2p_3}-\frac{1}{p_1p_2p_3})
\\&=(n-\frac{n}{p_1}-\frac{n}{p_2}-\frac{n}{p_3}+\frac{n}{p_1p_2}+\frac{n}{p_1p_3}+\frac{n}{p_2p_3}-\frac{n}{p_1p_2p_3})\end{split}$$

這展開之後跟集合寫成的樣子是一樣的，根據排容原理也間接證明公式的正確性！

## 程式實現

基本概念就是從2開始往後找所有的質因數，因為個數不重要，只要每一次找到之後更新答案即可。值得注意的是，最後必須對剩下的 $n$ 在做一次，其原因不難理解，當我有兩個質因數剩下還沒有被 i 走過的時候，因為兩個互質，因此必定有一個數會在迴圈內被走到，剩下那一個留下來的就會在外面被更新答案。

```cpp=
#include <bits/stdc++.h>
using namespace std;
int n,ans;

int phi(){
    ans = n;
    for(int i=2;i*i<=n;i++){
        if(n % i == 0){
            ans = ans - ans/i;
            while(n % i==0)n /= i;
        }
    }
    if(n > 1)ans = ans - ans/n;
    return ans;
}

int main(){
    cin>>n;
    cout<<phi()<<endl;
}
```

這是用程式跑出來的[1到10000的歐拉函數](https://gist.github.com/peienwu/39cf7c563bae22597ac1d185b12c535f)，可以參考看看！

## 好難的圖形

這張圖是歐拉函數的函數圖形，x軸是 $n$，y軸則是 $\phi(n)$。利用python的matplotlib模組繪製出來的！

以下為待解決問題：

- $(n,\phi(n))$ 的圖形中，斜率為 $1,\frac{1}{2},\frac{2}{3},\frac{4}{5},\frac{1}{3}$，其原因？
- 這些斜率是否可以求出數字落在每一條線上的機率？
- 函數總和的圖形，其方程式是如何推導的？
- 函數總和公式的誤差圖形呈現類似常態分佈，求其原因。
- 倒數總和的公式如何推導？
- 最後一個極限下界公式的推導，跟歐拉-馬斯刻若尼常數關係為何？

### 一般的 $n$ 與 $\phi(n)$ 關係圖

我覺得這東西超酷，有一條斜率為1的很明顯的直線，他代表的是 $\phi(n) = n-1$，這種情況就是當 $n$ 屬於質數的時候會發生。

$n$ 的範圍是1到150
![](https://i.imgur.com/ltC2iws.png)

$n$ 的範圍是1到1500
![](https://i.imgur.com/RuDTbyB.png)

$n$ 的範圍是1到10000
![](https://i.imgur.com/jqCNkkL.png)

#### 斜率 $\frac{1}{2}$ 的情況

這是嘗試解決問題的區域。

首先，我把斜率為 $0.5\pm0.01$ 的點全部描出來，大概就是這樣：
![](https://i.imgur.com/YbTOlb3.png)

小範圍跑出來，在100內一共有6個符合條件的數字，分別是：
> 2
> 4
> 8
> 16
> 32
> 64

哇！一切豁然開朗。當 $n$ 為 2 的冪次時，就只有一個 $2$ 是質因數，因此 $\phi(n) = n(1-\frac{1}{2}) = \frac{n}{2}$。但這樣的數字只會越來越少，但點卻沒有變得越來越稀疏，於是我們必須加大數字範圍觀察，[數字的列表在這](https://gist.github.com/peienwu/aeedeeb925a346f3f6e2cbef04a097ae)，丟到線上[質因數分解器](https://nap.st/prime_factorization_calculator/?lang=zh-TW#result)，會發現有很多數字都是有2個因數，其中一個為2，另外一個為一個較大的質數，數字越大對影響力就越小，因此他們會在斜率為 $\frac{1}{2}$ 的線上。

![](https://i.imgur.com/qNe9XyV.png)

像1964就會是$\frac{1}{2}\times \frac{490}{491}$，後面的影響很小，所有大致上接近 $\frac{1}{2}$。

#### 斜率 $\frac{1}{3}$ 的情況

紅線標出來的是斜率為 $\frac{1}{3}$ 的線條。
![](https://i.imgur.com/h1SdBSa.png)

情況大概就是這幾種，這些質因數算出來大概就是 $\frac{1}{3}$ 左右。
![](https://i.imgur.com/DBn0tXP.png)
![](https://i.imgur.com/TDIjZKr.png)

#### 斜率 $\frac{2}{3}$ 的情況

![](https://i.imgur.com/1ykO0vo.png)

這些數字很多事3乘上某個大質數。
![](https://i.imgur.com/OfSOsrW.png)

#### 斜率 $\frac{4}{5}$ 的情況

![](https://i.imgur.com/GmB7mdI.png)

一樣的情況，大多是5盛放某個大質數
![](https://i.imgur.com/xHLnmVr.png)

#### 來比較一下

![](https://i.imgur.com/sX2I4ry.png)

我們設定不同的 $n$ ，看在不同值域中個別斜率上面的點的數量，不過因為我設了小數點後兩位當作可接受的誤差範圍，因此會稍微多一點：

| 斜率/次數 | 1000 | 10000 | 100000 | 1000000 | 10000000    |
|:--------- | ---- |:----- |:------ |:------- | --- |
| 1         | 144  | 1205  | 10860  | 118298  |  1186321   |
| 1/2       | 152  | 1425  | 13531  | 141771  |  1401372   |
| 1/3       | 78   | 858   | 8031   | 82782   |  826322   |
| 2/3       | 67   | 721   | 6131   | 65638   |  664881   |
| 4/5       | 32   | 387   | 3281   | 33560   |  348334   |
| 其他      | 572  | 5404  | 58166  | 557951  |  5572770   |

| 斜率 | 點的占比 |
|:---- |:-------- |
| 1    | 11.86%   |
| 1/2  | 14.01%   |
| 1/3  | 8.26%    |
| 2/3  | 6.65%    |
| 4/5  | 3.48%    |
| 其他 | 55.73%   |

我想要計算一下他們變化的狀況：

![](https://i.imgur.com/XHbmuYD.png)
![](https://i.imgur.com/t2hmRro.png)

觀察圖形，好像有那麼一點會趨近於一個常數之類的，不過當數字超過 $10^7$ 之後，不管是用 C++或是 PYTHON 都跑不太出來。

```python=
import numpy as np
import matplotlib.pyplot as plt

def phi(n):
    ans = int(n)
    for i in range(2,n+1):
        if i*i > n:
            break
        if n % i == 0:
            ans = ans - ans/i
            while n % i == 0:
                n = n / i
    if n > 1:
        ans = ans - ans/n

    return ans

x = []
y = []
x2 = []
y2 = []
times = [1000000]
for j in range(1):
    cnt = 0
    for i in range(1,1000000+1):
        if i%100000 == 0:
            print("Now = ",i/100000)
        ans = int(phi(i))
        x.append(i)
        y.append(ans)
        if abs(ans/i-(4/5)) <= 0.01:
            x2.append(i)
            y2.append(ans)
            #print(i)
            cnt += 1
    print("Cnt = ",cnt)
        
#plt.vlines(x, 0, y, linestyle="-",linewidth=0.1)
#plt.plot(x,y,'o',markersize = 1)
#plt.plot(x2,y2,'o',markersize = 1,c = "red")
#plt.show()
```

```python=
import numpy as np
import matplotlib.pyplot as plt

def phi(n):
    ans = int(n)
    for i in range(2,n+1):
        if i*i > n:
            break
        if n % i == 0:
            ans = ans - ans/i
            while n % i == 0:
                n = n / i
    if n > 1:
        ans = ans - ans/n

    return ans

x = []
y = []
for i in range(1,1500+1):
    ans = int(phi(i))
    x.append(i)
    y.append(ans)
    
plt.vlines(x, 0, y, linestyle="-",linewidth=0.1)
plt.plot(x,y,'o',markersize = 1)
plt.show()
```

### 總和的關係圖

除此之外，這個東西也很酷，以下是 $f(n)$ 與 $n$ 的關係圖。滿足以下式子：

$$f(n) = \sum^n_{i=1}\phi(i)$$

![](https://i.imgur.com/DCeZ39L.png)

上網查後發現這一條線可以用一個關係式表達：
$$f(n) = \sum^n_{i=1}\phi(i) = \frac{3n^2}{\pi^2}$$

下圖藍線就是公式的圖，紅色的點則是 $\phi(n)$：
![](https://i.imgur.com/JZRTUeC.png)

```python=
import numpy as np
import math 
import matplotlib.pyplot as plt

def phi(n):
    ans = int(n)
    for i in range(2,n+1):
        if i*i > n:
            break
        if n % i == 0:
            ans = ans - ans/i
            while n % i == 0:
                n = n / i
    if n > 1:
        ans = ans - ans/n

    return ans

def func(x):
    s = (math.pi)*(math.pi)
    return 3*x*x/s
    

x = []
y = []
y2 = []

sum = 0
for i in range(1,101):
    ans = int(phi(i))
    sum += ans
    x.append(i)
    y2.append(func(i))
    y.append(sum)

plt.plot(x,y,'o',color = 'red',markersize = 2)
plt.plot(x,y2,color = 'blue',markersize = 0.1)
plt.show()
```

如果計算一下這一條理論線跟實際的誤差繪製出來的圖形：
$$\frac{1}{n}(\sum^n_{i=1}\phi(i)-\frac{3n^2}{\pi^2})$$

![](https://i.imgur.com/Bv80qbM.png)

很酷喔，當我把每一個誤差的數字當做x軸，y軸則是發生的次數，跑了1000000次之後，他的圖會出來會是像這樣，有點像常態分佈的東西（網路上是說埃爾米特多項式乘上高斯函數之類的東西）：
> The histogram has a distinctive shape, maybe hard to prove. I suspect it's the Gaussian Unitary Ensemble (a Hermite polynomial times a Gaussian).

![](https://i.imgur.com/i45SvDB.png)

```python=
import numpy as np
import math
import matplotlib.pyplot as plt

def phi(n):
    ans = int(n)
    for i in range(2,n+1):
        if i*i > n:
            break
        if n % i == 0:
            ans = ans - ans/i
            while n % i == 0:
                n = n / i
    if n > 1:
        ans = ans - ans/n

    return ans

def func(x):
    s = (math.pi)*(math.pi)
    return 3*x*x/s


x = []
y = []
box = [0]*2000

sum = 0

for i in range(1000):
    box[i] = 0
    
for i in range(1,10000000+1):
    ans = int(phi(i))
    sum += ans
    temp = int(((sum-func(i))/i)*2000)
    box[temp] += 1
    if i % 100000 == 0:
        print("Cur = ",i/100000)
    
for i in range(1400):
    x.append(i/2000)
    y.append(box[i])

plt.vlines(x, 0, y, linestyle="-",linewidth=0.4)
plt.plot(x,y,'o',markersize = 2)
plt.show()
```

### 倒數加總的圖形

下圖則是每一個 $\phi$ 的倒數繪製出來的圖形：
$$f(n) = \sum^n_{i=1}\frac{1}{\phi(i)}$$

![](https://i.imgur.com/7fm1u48.png)

很酷，但我一樣不知道怎麼解釋

### 取一堆怪怪的東西

還沒有結束，這一張圖表示的是 $n$ 對上 $\phi(n)\frac{\ln\ln n}{n}$ 呈現的圖形，據說會滿足以下關係式：

$$\lim_{n\to\infty}\inf\phi(n)\frac{\ln\ln n}{n} = e^{-\gamma}$$

指數次方上面是歐拉-馬斯刻若尼常數，畫出來的圖長這樣：

![](https://i.imgur.com/hA52Dxs.png)

一樣，公式有夠複雜！

```python=
import numpy as np
import math
import matplotlib.pyplot as plt

def phi(n):
    ans = int(n)
    for i in range(2,n+1):
        if i*i > n:
            break
        if n % i == 0:
            ans = ans - ans/i
            while n % i == 0:
                n = n / i
    if n > 1:
        ans = ans - ans/n

    return ans

x = []
y = []
for i in range(1,5000+1):
    ans = int(phi(i))
    x.append(i)
    temp = np.log(i)
    ans *= np.log(temp)
    
    y.append(ans/i)
    
plt.vlines(x, 0, y, linestyle="-",linewidth=0.05)
plt.plot(x,y,'o',markersize = 1)
plt.show()
```

<br>

**參考連結：**

- [連結1](https://mathworld.wolfram.com/TotientFunction.html)
- [連結2](https://mathworld.wolfram.com/TotientSummatoryFunction.html)
- [連結3](https://mathworld.wolfram.com/search/?query=Totient+Function&x=0&y=0)

## P2303 [SDOI2012] Longge 的问题

[題目敘述](https://www.luogu.com.cn/problem/P2303)
[Submission](https://www.luogu.com.cn/record/57881277)

這是歐拉函數簡單的應用，對於一個輸入 $n$ 要輸出的是 $\sum_{i=1}^n gcd(i,n)$，也就是每一個數字與 $n$ 的最大公因數總和。簡單的概念就是，$O(n)$ 枚舉所有的因數 $i$ 作為最大公因數，此時只要看與 $\frac{n}{i}$ 互質的數量，$i\times\phi(\frac{n}{i})$ 就會是以 $i$ 作為最大公因數的總和。

特別注意到，我們可以在枚舉 $i$ 的時候，也可以順便處理 $\frac{n}{i}$ 的情況，這樣就可以只枚舉 $\sqrt{n}$ 次就好了！

```cpp=
#include <bits/stdc++.h>
#define int long long
#define Orz ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n,ans = 0;

int phi(int n){
    int ans = n;
    for(int i=2;i*i<=n;i++){
        if(n % i == 0){
            ans = ans - ans/i;
            while(n % i==0)n /= i;
        }
    }
    if(n > 1)ans = ans - ans/n;
    return ans;
}

signed main(){
    Orz;
    cin>>n;
    int qt = sqrt(n);
    for(int i=1;i<=qt;i++){
        if(n % i == 0){
            ans += (n/i) * phi(i);
            if(i*i!=n)ans += i * phi(n/i);
        }
    }
    cout<<ans<<endl;
}
```
