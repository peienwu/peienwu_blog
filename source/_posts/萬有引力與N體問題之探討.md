---
title: 萬有引力與N體問體之探討
date: 2022-4-22
tags: 
    - 數學
    - 物理筆記
categories: 
    - 數學筆記
mathjax: true
---

此文將從萬有引力定律開始，在只考慮萬有引力的狀況下，探討衛星運動、雙星、三星、四星系統甚至是N體問題的軌跡與運動模式並進行模擬，並進行理論的驗證。

模擬連結：[點這裡](https://glowscript.org/#/user/ck1090758/folder/N-BodyProblem/)

## 理論與數學

### 萬有引力定律

牛頓於1687年提出：「**任何兩個質點之間必有一吸引力F，其量值和兩質量的乘積成正比，和它們之間的距離平方成反比，其方向在連心線上。**」

![](https://i.imgur.com/NsxdP20.png)

$$F = \frac{GMm}{r^2}$$

<!--more-->

### 圓周運動公式

高中選修物理提到，一個質量為$m$的物體以半徑$R$繞著圓心旋轉，角速度為$\omega$，其速度$v$、加速度$a$與週期$T$有以下公式，在分析衛星運動及多體運動中會常常用到。在行星運動當中，就是以彼此間的萬有引力當作向心力，進行圓周運動。

![](https://i.imgur.com/mDnymhI.png)

$$\omega = \frac{\Delta\theta}{\Delta t} = \frac{2\pi}{T}
\\v = R\omega = \frac{2\pi R}{T}
\\a = R\omega^2 =\frac{v^2}{R} =\frac{4\pi^2 R}{T^2}$$

### 橢圓性質

一個橢圓具有兩個焦點分別是下圖的$F_1,F_2$，其定義就是橢圓上任一點$P$到兩焦點的距離和固定，也就是$\overline{PF_1}+\overline{PF_1} = K$，$K$為定值。下圖中，$a$為半長軸，$b$為半短軸，滿足 $a^2 = b^2+c^2$，面積為 $\pi ab$。我們定義了一個決定橢圓扁平程度偏心率 $e = \frac{c}{a}<1$，偏心率愈大，橢圓愈扁平。

![](https://i.imgur.com/EHX9hdW.png)

也可以說，圓就是一個橢圓的特例，當$F_1,F_2$重合在一點的時候就變成了一個正圓。同時，橢圓軌跡能用以下方程式說明（可用距離公式證明）：

$$\frac{x^2}{a^2}+\frac{y^2}{b^2} = 1$$

平方相加容易聯想到三角函數的平方關係，因此我們可以假設 $x = a\cos\theta,y = b\sin\theta$，如此一來平方相加可得證。
由此可知，對於一個中心點為$(h,k)$的橢圓而言，其參數式為：

$$\left\{\begin{aligned} x &= h+a\cos\theta
\\y &= k+b\cos\theta\end{aligned}\right.$$

$$-\pi≤\theta≤\pi$$

### 克普勒三大行星運動定律

克普勒提出了行星三大運動定律，分別為：

1. **軌道定律**：所有行星各以太陽為焦點的橢圓形軌道上運動。
2. **面積定律**：太陽與行星的連線在相同時間間隔內掃過相等的面積。
3. **週期定律**：各行星繞行同一恆星，其公轉週期的平方與行星到太陽平均距離的三次方成正比。

下面主要會用到的是第一和第二定律，以及公式 $r_Av_A = r_Bv_B$進行證明。

![](https://i.imgur.com/c8iBRbi.png)

### 力學能守恆定律

一個質量為$m$的物體以速度$v$前進，與地心相距$r$，則該物體的動能與該處的重力位能為：
$$\begin{align}E_k &= \frac{1}{2}mv^2\\U &= -\frac{GMm}{r}\end{align}$$

在系統只有保守力做功下，其力學能保持不變。在分析以半徑$r$的等速率圓周運動的過程中，其力學能可以表示為：

$$E = U+E_k = \frac{-GMm}{r}+\frac{1}{2}m\frac{GM}{r} = \frac{-GMm}{2r}$$

由此我們可以得到：

$$E_k = \frac{-1}{2}U$$

## 行星與人造衛星運動

### 理論與數學

衛星質量$m$以軌道半徑$r$繞質量為$M$的行星行等速率圓周運動，其萬有引力恆指向$M$球心，作為圓周運動所需要的向心力。

$$F = \frac{GMm}{r^2} = ma_c = m\frac{v^2}{r}$$

由此可知，當有一個物體質量$m$在距離行星$r$處行等速率圓周運動，其速度大小$v = \sqrt{\frac{GM}{r}}$，速度方向與向心力方向垂直。在模擬過程中，有兩個很重要的要素，分別是初始的速度大小、以及速度的方向，有了這兩個東西以後，就能讓電腦以彼此之間的萬有引力進行模擬。

### 橢圓軌道與運動軌跡方程式

在實作的過程中，我發現到當速度 $v$ 比$\sqrt{\frac{GM}{r}}$大一點或小一點時，跑出來的軌跡就不會是正圓而是橢圓，讓我十分好奇能否在給定一個速度的情況下，計算出**橢圓軌跡方程式**。

下圖是初始狀態的設定，白球質量$M$、位置為原點、紅球質量$m$，紅球距離白球$H$、初始速度$v=k\sqrt{\frac{GM}{H}}$垂直向上，透過調整$k$的大小，預測其運動軌跡。顯然，當$k = 1$時軌跡會是正圓形，$k<1,k>1$則是分別以H為近日點、遠日點半徑的橢圓。

![](https://i.imgur.com/3LG2sic.png)

若近日點距為$r_1$、遠日點距為$r_2$，半軸長$a = \frac{r_1+r_2}{2}$，根據遠日點以及近日點的力學能守恆以及克卜勒第二定律，可以列出以下兩式：

$$\begin{aligned}\frac{1}{2}mv_1^2 - \frac{GMm}{r_1} &= \frac{1}{2}mv_2^2 - \frac{GMm}{r_2}\\\\
\frac{\pi ab}{T} = r_1v_1 &= r_2v_2\end{aligned}$$

將二式帶入一式可得：

$$(r_1v_1)^2 = \frac{GMr_1r_2}{\frac{1}{2}(r_1+r_2)} =
\frac{GM(a+c)(a-c)}{a} = \frac{GMb^2}{a}$$

帶入我們的初始速度與近、遠日點距：

$$(H\cdot k\sqrt{\frac{GM}{H}})^2 = k^2GMH=\frac{GMb^2}{a}$$

上面是第一式，還有兩條比較顯而易見的兩條方程式，總共用三條方程式解三個未知數：

$$\left\{\begin{aligned} \frac{b^2}{a} &= k^2H
\\a^2 &= b^2+c^2
\\a &= H-c\end{aligned}\right.$$

將三式帶入二式消去$c$，得到：

$$\left\{\begin{aligned} a &= \frac{H}{2-k^2}
\\b &= \sqrt{aHk^2}\end{aligned}\right.$$

有了$a,b$之後，就可以回推週期：

$$\begin{aligned}\frac{\pi ab}{T} &= \frac{1}{2}r_1v_1
\\\\\Longrightarrow \ T &= \frac{2\pi ab}{r_1v_1}\end{aligned}$$

最後，就可以用橢圓參數式畫出軌跡，並且完全吻合紅球的運動軌跡！

### 程式實作

程式連結：[點這裡](https://glowscript.org/#/user/ck1090758/folder/N-BodyProblem/program/%E8%A1%8C%E6%98%9F%E9%81%8B%E5%8B%95%EF%BC%BF%E6%A9%A2%E5%9C%93%E8%BB%8C%E9%81%93)

分別將$k$帶入不同數值，觀察軌跡的變化。左邊為模擬的狀況，右邊則是紅球動能（黑線）、位能（綠線）以及力學能（紅線）大小，由於只有保守力做功，其力學能總和保持不變。初始狀態如下：$M = 10,m = 1,H = 5$。

#### K = 0.6代入

![](https://i.imgur.com/8M7jZIW.png)

$$\left\{\begin{aligned}
a &= 3.0488
\\b &= 2.3426
\\c &= 1.9512\
\\T &= 4.0955\end{aligned}\right.$$

#### K = 1代入

![](https://i.imgur.com/vyLsudD.png)

$$\left\{\begin{aligned}
a &= 5.0000
\\b &= 5.0000
\\c &= 0.0000
\\T &= 8.6015\end{aligned}\right.$$

#### K = 1.3代入

![](https://i.imgur.com/dIZdlCU.png)

$$\left\{\begin{aligned}
a &= 16.1290
\\b &= 11.6743
\\c &= 11.1290
\\T &= 49.8344\end{aligned}\right.$$

#### K = 2代入

![](https://i.imgur.com/6RO0Svz.png)

$$\left\{\begin{aligned}
a &= -2.5000
\\b &= nan
\\c &= nan
\\T &= nan\end{aligned}\right.$$

這種情況是紅球脫離了黑球的重力束縛，一去不復返。可以看到，動能與位能呈現對稱縮小的狀況，動能逐漸趨近於總力學能，而重力位能逐漸趨近於零。

這裡發生了一個有趣的現象，當我把$k$繼續調小到足夠小，紅球會繞行一個非常扁平的橢圓軌跡，在現實世界中也就是紅球撞上白球的感覺。

反之，當我把$k$繼續往上調大時，會發現紅球脫離了白球的重力場而一去不復返，我試著找出這個臨界值，從我們上面的推導可知$a = H/(2-k^2),b = \sqrt{aHk^2}$，當$k>\sqrt{2}$時，$b$無解，也就代表這樣的橢圓形軌跡是不存在的。換句話說，當$v>\sqrt 2\cdot\sqrt{\frac{GM}{H}}$時，紅球會擺脫白球重力的影響，也就是**第二宇宙速度**或**脫離速度**。

由此可知，$|k|<\sqrt 2$。

{% note success %}
**第二宇宙速度**或**脫離速度**是指在地球上發射的物體擺脫地球重力束縛，飛離地球所需的最小初始速度。假設質量$m$的物體速度為$v$，則如果要擺脫地球重力的束縛，其動能要大於其重力位能，取最小值就是兩者相等，因此：

$$\frac{1}{2}mv^2 =\frac{GMm}{r}$$

得到脫離速度：

$$v = \sqrt{\frac{2GM}{r}}$$
{% endnote %}

很巧的是，我們可以從橢圓半短軸長的無解推出脫離速度，也能從力學能的方向角度切入，得到一樣的結果，可謂殊途同歸！

## 雙星運動

觀察兩個質量分別為$m1,m2$的物體，繞著彼此的質心進行週期運動。

![](https://i.imgur.com/qpkSshS.png)

### 理論與數學

令兩物體彼此間距離為$d$，則有以下的數學關係式：

|            |       $$m_1$$               |       $$m_2$$   |
|:----------:|:----------------------------------:|:----------------------------------:|
|  軌道半徑  | $$r_1 = \frac{m_2}{m_1+m_2}\cdot d$$ | $$r_2 = \frac{m_1}{m_1+m_2}\cdot d$$ |
| 向心加速度 |      $$a_1 = \frac{Gm_2}{d^2}$$      |      $$a_2 = \frac{Gm_1}{d^2}$$      |
| 軌道速率  | $$v_1 = m_2\sqrt{\frac{G}{d(m_1+m_2)}}$$  |   $$v_2 = m_1\sqrt{\frac{G}{d(m_1+m_2)}}$$   |

### 模擬實作

模擬連結：[點這裡](https://glowscript.org/#/user/ck1090758/folder/N-BodyProblem/program/%E7%90%86%E6%83%B3%E9%9B%99%E6%98%9F%EF%BC%BF%E5%90%8C%E5%BF%83%E5%9C%93%E8%BB%8C%E9%81%93)

#### 正圓雙星運動

正圓形的軌道中，軌道半徑與其質量呈反比。

![](https://i.imgur.com/48i6Oel.png)

$$\left\{\begin{aligned}
m1 &= 3.0000\\
m2 &= 1.0000\\
v1 &= 0.7455\\
v2 &= 2.2366\\\end{aligned}\right.$$

#### 橢圓雙星運動（質量不同）

橢圓形的軌道中，軌道半徑也會與其質量呈反比（此軌道半徑可以定義為半軸長，由微積分可得證）。

![](https://i.imgur.com/qcVlBQD.png)

$$\left\{\begin{aligned}
m1 &= 3.0000\\
m2 &= 1.0000\\
v1 &= 0.3728\\
v2 &= 1.1183\\\end{aligned}\right.$$

#### 橢圓雙星運動（質量相同）

![](https://i.imgur.com/6G4Djws.png)

$$\left\{\begin{aligned}
m1 &= 1.0000\\
m2 &= 1.0000\\
v1 &= 0.5272\\
v2 &= 0.5272\\\end{aligned}\right.$$

## 三星運動

再研究天體力學上，**雙星問題**（Two-Body Problem）是已經被證明有精確解，但對於**三體問題**（Three-body problem）甚至是**多體問題**（N-body problem）問題就變得特別複雜，除特別案例以外，並沒有辦法能精確描述各物體運動的行為。儘管如此，此系統還是會遵守動量守恆，其質心不會移動。

三星問題困難的地方在於，往往初始非常微小的誤差，就有可能造成之後完全不同的結果，變得混亂而不可預測，也就是[混沌狀態](https://en.wikipedia.org/wiki/Chaos_theory)。

### 理論與數學

由於三體問題過於複雜，我們簡化成將三個物體的質量都設為$m$，放在邊長為$L$的正三角形三個頂點上，與質心距離$r = \frac{\sqrt3}{3}L$，則彼此間的萬有引力取餘弦將作為繞質心旋轉所需要的向心力：

$$\sqrt 3\frac{Gm^2}{L^2} = ma_c = m\frac{v^2}{r} = m\frac{4\pi^2r}{T}$$

可得到以下：

$$\left\{\begin{aligned}
v &= \sqrt{\frac{Gm}{L}}\\
T &= 2\pi\sqrt{\frac{L^3}{G(3m)}}
\end{aligned}\right.$$

### 正圓軌跡三星運動模擬

模擬連結：[點這裡](https://glowscript.org/#/user/ck1090758/folder/N-BodyProblem/program/%E7%90%86%E6%83%B3%E4%B8%89%E6%98%9F%EF%BC%BF%E6%AD%A3%E5%9C%93%E8%BB%8C%E9%81%93)

正三角形的三個頂點以相對位置固定、共同質心為旋轉中心進行旋轉。透過彼此的萬有引力餘弦結果當作向心力，設定好初始速度大小與方向（分別在90度、210度、300度），就能讓電腦完成剩下的工作！

![](https://i.imgur.com/fltbWQf.png)

### 橢圓軌跡三星運動模擬

模擬連結：[點這裡](https://glowscript.org/#/user/ck1090758/folder/N-BodyProblem/program/%E4%B8%89%E6%98%9F%E9%81%8B%E5%8B%95%EF%BC%BF%E6%A9%A2%E5%9C%93%E8%BB%8C%E9%81%93)
影片連結：[點這裡](https://youtu.be/fJpKcJ9cGFI)

此為不穩定的模擬，也就是說在一段時間過後，整個系統會失序，呈現混亂的狀態。

![](https://i.imgur.com/bOGPPVW.png)

### 其他軌跡的三星運動

其他三體互繞的軌跡如八字型，就被證明出來是一個穩定的運動模式。

![](https://i.imgur.com/33rrTPs.gif)

以及衛星的五個拉格朗日點，可以在下圖五個位置放入第三個不考慮其質量的物體，使其與其他星體的相對位置不變。

![](https://i.imgur.com/kQhIG3Q.png)

## 四星運動

在一堂物理課，老師教到三星系統時，順便提及了四星運動應該以什麼樣的性狀、什麼方式運動。若要保持四個物體等距，唯一的方式就是把他們放在正四面體上的四個頂點上，以正四面體之外心為旋轉中心進行旋轉

### 理論與數學

假設四個質量均為$m$的物體，分別落在正四面體的四個頂點上，若以正六面體思考正四面體，正四個頂點分別是$(0,0,0),(1,1,0),(1,0,1),(0,1,1)$

![](https://i.imgur.com/xpaRkjF.png)

如下圖，假設$A,B,C,D$上均有一個質量為$m$的物體，我們針對$B$分析其受力狀況（其他點其實是一樣的）。可以看到，$B$受到了分別指向$A,B,D$三點、大小為$\frac{Gm^2}{L^2}$的力，將這些力在指向質心$M$方向做分力。

![](https://i.imgur.com/NpWDJas.png)

1. **水平方向（ABC平面）**：

$$2\times\frac{Gm^2}{L^2}\cdot\cos30^{\circ}+\frac{Gm^2}{L^2}\cdot\cos\theta$$

2. **垂直方向：**

$$\frac{Gm^2}{L^2}\cdot\sin\theta$$

分析正四邊形的長度性質，由於四面體高度是$h = \frac{\sqrt6}{3}$、正三角形外接圓$\frac{\sqrt3}{3}$，我們可以得到$\sin\theta = \frac{\sqrt3}{3},\cos\theta = \frac{\sqrt 6}{3}$，代入上式可得並做畢氏定理可得：

$$\begin{align}F_B &= \frac{Gm^2}{L^2}\sqrt{(\sqrt3+\frac{\sqrt3}{3})^2 + (\frac{\sqrt6}{3})^2}\\
&=\sqrt6\cdot\frac{Gm^2}{L^2}\end{align}$$

正四面體的外接圓半徑$R = \frac{\sqrt6}{4}L$，因此可得：

$$\frac{mv^2}{R} = \sqrt6\cdot\frac{Gm^2}{L^2}$$

最後得到軌道速度與旋轉週期：

$$\begin{align}v &= \sqrt{\frac{3Gm}{2L}}\\
T &= 2\pi\sqrt{\frac{L^3}{G\cdot(4m)}}
\end{align}$$

### 程式實作

模擬連結：[點這裡](https://glowscript.org/#/user/ck1090758/folder/N-BodyProblem/program/%E5%9B%9B%E6%98%9F%E9%81%8B%E5%8B%95)
影片連結：[點這裡](https://youtu.be/fNabqv4BYKg)

有了速度大小以後，接下來就是初始速度方向的設定。這一部分特別困難，以正四面體的四星在立體空間的運動，旋轉的角度若沒有抓好，很有可能整個結構會崩塌。例如下圖情況，前後兩個物體會因為萬有引力作用而不斷吸引：

![](https://i.imgur.com/oSjvetd.png)

嘗試解決的方法是，將正四面體的四個頂點座標投影到$xy$平面，因為四個頂點是$z$座標極大或極小，如此一來就能讓所有速度方向的$z$分量為零。接著，只要將速度方向依照下圖分解即可：

![](https://i.imgur.com/2xo2a8N.png)

可以看到下圖左方有兩張圖，上圖是四顆球的總動能、位能與力學能的圖、下圖是黃球與其他球的距離（黑線是與質心距離）。在理想的狀況下，我們預期所有球的相對位置應該保持不變，但在模擬中卻是呈現週期性的變化，並且可以觀察球與質心的距離並不是固定的，而是有上下起伏的變化。

![](https://i.imgur.com/xAtoSH5.png)

轉了差不多十圈後，會發現四星系統呈現混沌狀態，分析原因可能有以下兩個：

1. **精度問題**：初始狀態速度方向向量精度不足，初始的些許差別導致後續的混亂。
2. **模擬本身限制**：我們採用的是對時間微分進行每一個瞬間的力學分析，時間間隔大小也會影響到整個模擬的結果。

![](https://i.imgur.com/hHWFWIf.png)

這也讓我們思考一個蠻有趣問題，是否真的存在一個穩定的四星系統彼此能在相對位置固定的狀況下進行運動？

## N體問題運動模擬

模擬連結：[點這裡](https://glowscript.org/#/user/ck1090758/folder/N-BodyProblem/program/N%E9%AB%94%E5%95%8F%E9%A1%8C)

最後就是N體問題的模擬，將50個質量為$m$的物體在隨機生成的位置上，彼此受到萬有引力運動。右圖的黑線為50個物體的總動能、綠線為彼此的位能，紅線為兩者相加，也就是總力學能。在沒有外力作用的狀況下，能由右圖看出來力學能是守恆的！

![](https://i.imgur.com/dVP3ywW.png)

## 總結與延伸問題

從萬有引力的理論與數學出發，整理行星的運動軌跡、雙星、三星、四星甚至到N星的運動模式以及模擬，讓我們能從不一樣的角度切入物理問題。這邊還有一個十分有趣的問題尚未解決，也就是**是否存在一個穩定的四星系統**？在大部分的N體問體的情況中，都沒有辦法得到一個穩定的運動模式，總會因為起始狀態的小小誤差導致截然不同的結果，穩定的四星系統也就變成蠻難解決的問題。

## 參考資料

* [Wiki:Two-body problem](https://en.wikipedia.org/wiki/Two-body_problem)
* [Wiki:Three-body problem](https://en.wikipedia.org/wiki/Three-body_problem)
* [Wiki:N-body problem](https://en.wikipedia.org/wiki/N-body_problem)
* [科學online：力學能(Mechanical energy)
](https://highscope.ch.ntu.edu.tw/wordpress/?p=47998)
* 物理講義：第八、九章
