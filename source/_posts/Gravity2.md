---
title: 橢圓軌道上行星到太陽平均距離
date: 2022-4-21
tags: 
    - 數學
    - 物理筆記
categories: 
    - 數學筆記
mathjax: true
---

## 簡介

克卜勒第三運動定律說明公轉週期的平方與行星到太陽的平均距離三次方成正比。若是行星以橢圓軌道繞行太陽，則行星到太陽的平均距離 $\overline d$ 會是近日點距 $r_1$ 加上遠日點距 $r_2$ 的平均，也就是橢圓的半長軸，讓我十分好奇該如何證明這件事！

$$\overline d = \frac{r_1+r_2}{2} = a$$

所要做的是就是將橢圓圓周上的每一點，到太陽的距離進行積分後取平均。

<!--more-->

![](https://i.imgur.com/vJtmR1W.png)

## 數學證明

定義連續函數$f(x)$在$[a,b]$區間上的平均值為：

$$f_{ave} = \frac{1}{b-a}\int_{a}^{b} f(x) dx$$

在一個以原點為中心的橢圓周上有一點$P$，其參數式可以表示成$P(a\cos\theta,b\sin\theta)$，其中 $a,b$ 為半長軸、半短軸。令太陽的座標為橢圓的其中一個焦點$S(-c,0)$，如下圖：

![](https://i.imgur.com/b4PWR3b.png)

則橢圓周上每一點$P$到太陽$S$的距離可以表示為：

$$f(x) = \overline{PS} = \sqrt{(a\cos(x)+c)^2+(b\sin( x))^2}$$

如此一來就可以對$f(\theta)$在$[0,2\pi]$區間內取平均值：

$$\begin{align}f_{ave} &= \frac{1}{2\pi}\int_0^{2\pi} f(x)dx\\
&=\frac{1}{2\pi}\int_0^{2\pi}{\sqrt{(a\cos(x)+c)^2+(b\sin(x))^2}}\ dx\\
&=\frac{1}{2\pi}\int_0^{2\pi}{\sqrt{a^2\cos^2(x)+2ac\cdot\cos(x)+(a^2-c^2)\cdot\sin^2(x)+c^2}\ }dx\\
&=\frac{1}{2\pi}\int_0^{2\pi}{\sqrt{c^2\cos^2(x)+2ac\cos(x)+a^2}}\ dx\\
&=\frac{1}{2\pi}\int_0^{2\pi}{(c\cdot\cos(x)+a)}\ dx\\
&=\frac{1}{2\pi}[c\int_0^{2\pi}{\cos(x)}\ dx+\int_0^{2\pi}a\ dx]\\
&=\frac{1}{2\pi}\cdot(0 + 2\pi a)\\
&=a
\end{align}$$

得證橢圓軌道上地球到太陽的平均距離為半軸長！

## 程式實作

這次主要是以程式實作積分，透過將角度切成很小塊再疊加起來取平均，得到函數的平均值。以下影片中的橢圓$a = 13,b = 5,c = 12$，透過將角度切成30度、15度、1度、0.5度觀察疊加的狀況。

### 角度 = 30度

<iframe width="560" height="315" src="https://www.youtube.com/embed/gb37pmX-P6w" frameborder="0" allowfullscreen></iframe>

### 角度 = 15度

<iframe width="560" height="315" src="https://www.youtube.com/embed/hWMe3eOZVyU" frameborder="0" allowfullscreen></iframe>

### 角度 = 1度

<iframe width="560" height="315" src="https://www.youtube.com/embed/OpcFig66emQ" frameborder="0" allowfullscreen></iframe>

### 角度 = 0.5度

<iframe width="560" height="315" src="https://www.youtube.com/embed/2C0RBenG2yg" frameborder="0" allowfullscreen></iframe>

這四個影片加起來的平均值都是半長軸$a$！