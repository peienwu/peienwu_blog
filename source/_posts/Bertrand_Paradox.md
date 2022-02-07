---
title: Bertrand Paradox with Simulations
date: 2022-2-7
tags: 
    - 數學
    - 機率
categories:
	- 數學筆記

mathjax: true
---

## 簡介

Bertrand Paradox是Bertrand他在他的一本書中提到關於機率論的悖論，他以「在圓上產生隨機的弦」來說明當沒有嚴謹的定義好產生隨機的「方法」，就有可能產生許多合理但結果卻完全不同的答案。

悖論如下：給定一個平面上的圓，內接一個邊長為 $T$ 的正三角形。接著隨機產生園內的一條弦，想問這條弦的長度大於 $T$ 的機率有多大？
<!--more-->
以下三個方法會分別產生出三個不同的結果，影片、圖片部分是用*manim*以及*matplotlib*繪製而成的。


## 產生弦的不同方法

### 方法1：隨機端點

這是最直覺產生弦的方法，隨機選擇圓上的兩個點連成一條弦。如下圖，不失一般性假設其中一個點是$A$，則另外一個點可能落在$A1,A2,A3$的弦上。若要產生大於三角形邊長的弦，則必須落在$A3$上，其機率為$\frac{1}{3}$。

![](https://i.imgur.com/VY5lu3v.png)

以下影片為模擬隨機產生弦的狀況，弦長度大於 $T$ 機率約為$0.33$左右。

{%youtube dJB3xVKWavI %}

將產生的弦描繪出來如下圖：
<img src="https://i.imgur.com/gbpWKKe.png" alt="drawing" width="300"/>

### 方法2：隨機半徑

此作法是隨機產生一條半徑，假設其與圓周交於$A$，接著隨機在半徑上找一點，做出過此點且與半徑垂直的弦。我們可以發現，當點的位置下圖綠色線段上時，弦的長度大於 $T$，在黃色線段時則小於 $T$。
![](https://i.imgur.com/RZ97qY7.png)

黃色與綠色線段長度相等，因此產生大於 $T$ 的弦長機率為$\frac{1}{2}$，約莫$0.5$左右。

{%youtube sH-0lX4h1lw %}

將產生的弦描繪出來如下圖：

<img src="https://i.imgur.com/UGawzWw.png" alt="drawing" width="300"/>


### 方法3：隨機中點

此作法是在園中隨機產生一個點，以此點為中點產生一條弦。以下圖來說，假設此點在小圓內，則弦長會大於 $T$，在小圓外則小於 $T$，若恰好落在小圓圓週上，則弦長等於 $T$。

由於小圓的半徑是大圓半徑的一半（可以假設大圓半徑$R$，$T = \sqrt3R$，小圓圓心落在三角形重心上證明），小圓大圓面積比為$1:4$，因此長度大於 $T$ 的弦機率為$\frac{1}{4}$。

![](https://i.imgur.com/fLgOuOC.png)

透過模擬得到機率約為$0.25$左右，當樣本數越高，機率會越精準。

{%youtube oF2DIPIAFcU %}

將產生的弦描繪出來如下圖，跟前面兩個比起來，通過圓中心附近的弦數量較少：

<img src="https://i.imgur.com/kOaKQCL.png" alt="drawing" width="300"/>


## 三者比較

利用*matplotlib*畫出每條弦的中點並留下軌跡，可以得到下面的圖形，隨機端點與隨機半徑產生的圖形比較相近，集中在圓的中心，而隨機中點的分佈則十分平均。

### 方法1：隨機端點

![](https://i.imgur.com/C1zPHS4.png)

### 方法2：隨機半徑

![](https://i.imgur.com/riz3ZQn.png)


### 方法3：隨機中點

![](https://i.imgur.com/5M2cLna.png)

在沒有給定額外資訊的情況下，並沒有哪一種方法才是所謂正確解答，不存在唯一的選擇方法，那麼也就不存在一個唯一的解答。

## 參考資料

此篇文章的目的在熟悉*manim*製作動畫以及*matplotlib*的操作，內容皆為已知喔。

[Github: Code and Photos](https://github.com/peienwu/cp_code/tree/main/Bertrand%20Paradox)

[Bertrand's Paradox (with 3blue1brown) - Numberphile](https://youtu.be/mZBwsm6B280)

[Wiki: Bertrand paradox](https://en.wikipedia.org/wiki/Bertrand_paradox_(probability))