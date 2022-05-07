---
title: 錯排問題（Derangements）
date: 2021-7-10
tags: 
    - 數學
    - 2021暑假筆記
categories: 數學筆記
mathjax: true
---

## 定義

錯排是組合數學中的問題之一。考慮一個有n個元素的排列，若一個排列中**所有的元素都不在自己原來的位置上**，那麼這樣的排列就稱為原排列的一個錯排。本文將從**錯排公式**的推導開始，近一步探究其性質及應用。

$n$個元素的錯排數記為 $D_n$ 或 $!n$。 研究一個排列錯排個數的問題，叫做錯排問題或稱為更列問題。
<!-- more -->
## 規律與公式

### 反面枚舉

前幾項可以用取捨原理（或窮舉所有可能）求得：
$$\begin{align}D_1 &= 0 \\ D_2 &= 1\\D_3 &= 2 \\ D_4 &= 9\end{align}$$

{% note default %}
**三個元素的錯排，以下共兩種：**
![](<https://i.imgur.com/24cJS8t.png> =500x)

**四個元素的錯排，以下共9種：**
> 4123
> 3421
> 3142
> 4312
> 2413
> 2341
> 4321
> 3412
> 2143
{% endnote %}

如下面的文氏圖可以看到，以三個元素的錯排為例，我們要求的就是綠色區域的數量，透過排容原理（取捨原理）來算。
![](<https://i.imgur.com/4cSFDiw.png> =400x)
$$\begin{split}D_3 = 3!-C^3_1\cdot2!+C^3_2\cdot1!-C^3_3\cdot0! =2\end{split}$$
如果要求 $!4$ 也是一樣的作法：
$$D_4 = 4!-C^4_1\cdot3!+C^4_2\cdot2!-C^4_3\cdot1!+C^4_4\cdot0! = 9$$

這邊可以觀察到一個重要的規律，階乘的遞減、C取的數字遞增以及正負交替，這些性質會被用在等一下的一般化推導！

### 遞迴解

第一個公式是高中數學利用遞迴的概念得到的：
$$D_n=(n-1)(D_{n-1}+D_{n-2})$$

{% note success %}
**證明：**
在原本 $n-1$ 個元素當中取一個元素跟新加入的元素（第n個元素）做交換，共有$C^{n-1}_1$ 種取法，假設取到的元素是K，則K有以下兩種情況：

1. K本身即不在原本的位置上，跟新加入的元素調換亦是錯排
2. K在原本的位置上，跟新加入的元素調換後形成錯排
![](https://i.imgur.com/k7l4Srr.png)

綜合以上兩種狀況，可以推得上面的公式
{% endnote %}

第二個是維基百科上面的公式：
$$D_n = nD_{n-1}+(-1)^n$$
這一個公式可以由等一下的一般式的公式（下面的公式解）推討過來：
$$\begin{split}D_n&=n!\sum_{k=0}^{n}\frac{(-1)^k}{k!}\\&=n(n-1)!\sum_{k=0}^{n-1}\frac{(-1)^k}{k!}+n!\cdot\frac{(-1)^n}{n!}\\&=nD_{n-1}+(-1)^n\end{split}$$

用這兩個遞迴式可以推得第n項的數值，不過時間複雜度依然是$O(n)$，想要求得的是$O(1)$的公式解

### 公式解

公式解可以利用取捨原理求得，寫成一般化的形式。對於第n項的錯排數可以利用之前觀察到排容原理的規律整理出以下式子：
$$\begin{split}D_n&=n!-C^n_1(n-1)!+C^n_2(n-2)!-\cdots+C^n_n0!\\&=\sum^n_{k=0}C^n_k(n-k)!\cdot(-1)^k\\&=\sum_{k=0}^{n}\frac{n!}{k!(n-k)!}\cdot(n-k)!\cdot(-1)^k\\&=n!\sum^{n}_{k=0}\frac{(-1)^k}{k!}\end{split}$$

這個就是錯排數的一般化公式，如果想要把sigma消掉，還可以用以下處理：
$$\begin{split}D_n&=n!\sum^{n}_{k=0}\frac{(-1)^k}{k!}\\&=n![1-\frac{1}{1!}+\frac{1}{2!}-\cdots+(-1)^n\frac{1}{n!}]\end{split}$$

根據泰勒公式（$R_n$為修正項）
$$\begin{split}e^{-1}&=1+\frac{(-1)^1}{1!}+\frac{(-1)^2}{2!}+\cdots+(-1)^n\frac{1}{n!}+R(n)\\&=[1-\frac{1}{1!}+\frac{1}{2!}-\cdots+(-1)^n\frac{1}{n!}]+R(n)\end{split}$$

因為泰勒公式，中括號內的數值在n越大時會越接近$e^{-1}$，因此可以改寫成以下式子：
$$\begin{split}D_n \cong n!\cdot e^{-1}\to\frac{D_n}{n!}\cong e^{-1}\end{split}$$

這就是n樣物品錯排的機率，那因為這只是近似值，由於正負交替，數值會在$e^{-1}$ 的地方震盪。為了要求出確切的$D_n$的解，我們要在後面加上一個修正項（泰勒展開餘項$R_n$），經過整理發現這個餘項 $\frac{1}{n+1}$ 會嚴格小於 $0.5$，因此得到以下的一般項公式：
$$D_n=\lfloor\frac{n!}{e}+0.5\rfloor$$

以上就是對於一般項$D_n$ 的一般項公式！

## 實際模擬

利用ggb的模擬做出震盪的效果，我是直接輸入一般化的公式，但討論n不是正整數的情況其實沒有意義，不過程式還是可以跑出一個數值如下：
![](https://i.imgur.com/0mTp7jo.png)

最後會趨近於$e^{-1}\cong0.36787944$
因為我們討論的都是整數解的狀況，利用python 模擬出正整數解的情況：
![](https://i.imgur.com/VE4q8gh.png)

印出n從一到20的跟$e^{-1}$的差距，可以看到有正負交替的情況，且每一次的差距都越來越小：
![](https://i.imgur.com/dloPsbD.png)

以上都是近似的情況，因為公式是一個無窮級數，只會越來越接近理論的機率 $e^{-1}$ ，但永遠都會有一個微小的差距，也就是無限逼近的概念！

```python=
import numpy as np
import matplotlib.pyplot as plt

const = np.exp(-1)

l = [0 for i in range(100)]
l[0] = 1
for i in range(1,21):
    l[i] = l[i-1]*i

a = [0 for i in range(100)]
a[0] = 0
a[1] = 0
a[2] = 1
a[3] = 2
a[4] = 9

for i in range(5,21):
    a[i]= (a[i-1]+a[i-2])*(i-1)

x = []
y = []
for i in range(1,21):
    x.append(i)
    y.append((a[i]/l[i]))
    print((a[i]/l[i])-const)

plt.title("Possibility to N", fontsize=20) #圖表標題
plt.xlabel("N", fontsize=16) #x軸標題
plt.ylabel("Possibility", fontsize=16) #y軸標題

plt.xticks([0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20])
plt.yticks([0,0.1,0.2,0.3,0.4,0.5,0.6])

plt.plot(x,y)
plt.show() #顯示繪製的圖形
```

## 題目—P3182 [HAOI2016]放棋子

[題目連結](https://www.luogu.com.cn/problem/P3182)
題目敘述是這樣：
> 給你一個 N×N 的矩陣，每行有一個障礙，數據保證任意兩個障礙不在同一行，任意兩個障礙不在同一列，要求你在這個矩陣上放 N 枚棋子（障礙的位置不能放棋子），要求你放 N 個棋子也滿足每行只有一枚棋子，每列只有一枚棋子的限制，求有多少種方案。
第一行一個N，接下來一個N*N的矩陣。n≤200，0 表示沒有障礙，1 表示有障礙，輸出一個整數，即合法的方案數。

這一題是在整理錯排相關公式的時候發現這一題，很明顯的，障礙物代表原來的位置，所以其實要求的就是n個元素的錯排種類。因為這一題的$n≤200$，第200項的值會接近$200!$的量級，[大約是$10^{374}$ 這麼多](https://www.wolframalpha.com/input/?i=floor%28%28200%21%2Fe%29%2B0.5%29)，對大數加法乘法來說卻是輕而易舉！
可以用$$D_n = nD_{n-1}+(-1)^n$$的遞迴公式，時間複雜度$O(n)$的時間（把大數運算當作$O(1)$但其實它的常數算大）求出答案！

如果想看從3個元素到200個元素的錯排總數，[可以點這裡！](https://gist.github.com/peienwu/7a7b5e72e8fd5cb0bc02977938eedc69)

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n,c[1000],lenc=0;

int power(int x){
    if(x%2==0)return 1;
    else return -1;
}

void calculate(int s,string x){
    int lenn = x.size(),product[lenc+lenn+10],brr[lenn+5];

    memset(product,0,sizeof(product));
    memset(brr,0,sizeof(brr));

    for(int i=0;i<lenn;i++)brr[i] = x[lenn-i-1]-'0';

    for(int i=0;i<lenn;i++){
        for(int j=0;j<lenc;j++){
            product[i+j] += brr[i]*c[j];
            if(product[i+j] >= 10){
                product[i+j+1] += product[i+j]/10;
                product[i+j] %= 10;
            }
        }
    }
    for(int i=lenc+lenn+9;i>=0;i--){
        if(product[i]!=0){
            lenc = i+1;
            break;
        }
    }
    for(int i=0;i<lenc;i++)c[i] = product[i];

    c[0] += power(s);
    if(c[0]==10){
        c[0] = 0;
        c[1] += 1;
        int ind = 1;
        while(c[ind]>=10){
            c[ind+1]+=1;c[ind] = 0;
            ind++;
        }
    }
    else if(c[0]==-1){
        c[0] = 9;
        c[1] -= 1;
        int ind = 1;
        while(c[ind]<=-1){
            c[ind+1]-=1;c[ind]=9;
            ind++;
        }
    }
}

int main(){
    ios;
    cin>>n;
    memset(c,0,sizeof(c));
    if(n==1)cout<<0<<endl;
    else if(n==2)cout<<1<<endl;
    else{
        c[0] = 1;lenc=1;
        for(int i=3;i<=n;i++){
            string temp = to_string(i);
            calculate(i,temp);
        }
        for(int i=lenc-1;i>=0;i--)cout<<c[i];
        cout<<endl;
    }
}
```

## 結論

整理一下上面提到錯排數的公式：

### 遞迴

$$D_n = (n-1)(D_{n-1}+D_{n-2})\quad,n\ge3\\D_n = nD_{n-1}+(-1)^n\quad,n\ge2$$

### 一般項公式解

$$D_n=\lfloor\frac{n!}{e}+0.5\rfloor\quad,n\ge1\\D_n=n!\sum^{n}_{k=0}\frac{(-1)^k}{k!}\quad,n\ge1$$

分析錯排問題所用到的指數函數與泰勒展開會在以後學到的時候把相關東西補起來，在這之前就先接受結論吧！
**歐拉：「錯排問題組合數學中的一個奇妙問題」**，實際查找資料發現到高中教錯排也只是淺淺的帶過公式，並沒有很深刻的對這個問題做討論（也算超出範圍啦），就想說對這個主題做更深入的一些探討！

## 附錄

一些的數學證明：
![](https://i.imgur.com/oMHPwZR.jpg)

要用python 繪圖之前要先在mac終端安裝一些東西：

```=
python3 -m pip install numpy
python3 -mpip install matplotlib
```
