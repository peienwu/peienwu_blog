---
title: 以VPython模擬三維空間中的反射定律
date: 2022-3-13
tags: 
    - VPYTHON
    - 數學
    - 模擬
categories:
    - 程式模擬
mathjax: true
---

![](https://i.imgur.com/Qt5Ktgo.png)

## 簡介

上週補習班開始教「平面方程式」，有一題範例感覺特別的酷，題目如下：

> 空間坐標系中，有一平面鏡E，一雷射光線經過點A(1,-1,2)射向鏡面E上的點B(0,1,0)，反射又經過點C(4，-3，2），試求平面E的方程式為何？

<!--more-->

![](https://i.imgur.com/CLng02c.png)

解法大概就是利用菱形對角線平分的概念求出平面方程式！由這一題延伸，我想看看當改變已知平面的各項參數時，反射光的向量會有怎麼樣的變化，於是就嘗試用VPython模擬出來！

## 反射定律及數學

平面的情況很好理解，入射角等於反射角，但當來到三維時，除了角度一樣之外，這三個向量還必須在同一個平面上

<img src="https://i.imgur.com/mub19sF.png" width="400">
![]( =400x)

原題我們是利用已知的$\overrightarrow{BA},\overrightarrow{BC}$，利用菱形邊長相等且平分夾角的性質，將長度調整成一樣之後相加即可求出平面的法向量，進一步求得反射的平面

![](https://i.imgur.com/pL9itZU.png)

要利用$\overrightarrow{BA}$以及平面方程式逆推反射光向量，在確認法向量的方向向量之後，將平面法向量的長度設為$|\vec{n}| = 2\overline{AB}\cos\theta$，由$\vec n = \overrightarrow{BA}+\overrightarrow{BC}$即可推得與$\overrightarrow{BA}$長度相同的$\overrightarrow{BC}$！

![](https://i.imgur.com/Aysupto.png)

綜合以上，令反射平面方程式為$ax+by+cz = 0$，$\vec n = (a,b,c)$，入射角為$\theta$，則：

$$\begin{split}\overrightarrow{BC} &= \frac{2|\overrightarrow{BA}|\cos\theta}{|\vec n|}\vec n - \overrightarrow{BA}
\\&=\frac{2|\overrightarrow{BA}|(\vec n\cdot\overrightarrow{BA})}{|\vec n|^2\,\overrightarrow{BA}}\vec n - \overrightarrow{BA}\end{split}$$


## 模擬及程式碼

### 使用說明

[模擬連結](https://glowscript.org/#/user/ck1090758/folder/%E4%BA%8C%E5%B9%B4%E7%B4%9ATEST/program/mirrorreflectionsimulation)
他會要求輸入發射源的$x,y,z$座標，完成後可以利用鍵盤'A,S'鍵調整平面$ax+by+cz=0$上$a$的大小（A變大、S變小），$b$則是'B,N'鍵、$c$是'C,V'鍵。

![](https://i.imgur.com/BrP36SB.gif)



利用以上的公式，我們建立各個物件、包含各個座標軸、反射平面以及入射反射光。模擬中兩個黃色的箭頭代表入射光及反射光的向量，灰色箭頭則是平面的法向量。

### 計算

計算夾角用到內積算夾角的餘弦$\cos\theta = \frac{\vec a\cdot\vec b}{|\vec a||\vec b|}$，算內積函式如下：

```python=
def dt(a,b):
    return a.x*b.x+a.y*b.y+a.z*b.z
```

接著就是計算$|\vec n|$的長度sz，對$\vec n$取單位向量後乘上長度，並設為平面的法向量，將$\vec n$與入射光向量相減得到反射光向量
```python=
    sz = 2 * mag(ball.pos) * dt(plane.up,ball.pos) / (mag(plane.up)*mag(ball.pos))
    n = norm(plane.up) * sz
    
    plane.up = vec(a,b,c)
    normal.axis = n
    
    arr_out.axis = n - ball.pos
```

### 完整程式碼

```python=
GlowScript 3.2 VPython

x = int(input("x = "))
y = int(input("y = "))
z = int(input("z = "))

a = 0
b = 1
c = 0

scene = canvas(width=1000, height=500,center = vector(0,0,0),range = max(x,max(y,z))+10) #設定畫面


def dt(a,b):
    return a.x*b.x+a.y*b.y+a.z*b.z

plane = box(pos=vector(0,0,0),length=20, height=0.1, width=10)

arrx = arrow(pos=vector(-10,0,-5), axis=vector(20,0,0), shaftwidth=0.1, color=color.red)
arry = arrow(pos=vector(-10,0,-5), axis=vector(0,20,0), shaftwidth=0.1, color=color.blue)
arrz = arrow(pos=vector(-10,0,-5), axis=vector(0,0,20), shaftwidth=0.1, color=color.green)

ball = sphere(pos=vector(x,y,z),radius=0.5,color = color.red)

arr_in = arrow(pos=ball.pos, axis=plane.pos-ball.pos, shaftwidth=0.5, color=color.yellow)
arr_out = arrow(pos=vec(0,0,0), axis=vector(0,0,0), shaftwidth=0.5, color=color.yellow)
normal = arrow(pos=vec(0,0,0), shaftwidth=0.1, color=color.white)

label_a = label(pos=vec(max(x,max(y,z))+10,10,0), box = 0 ,height = 20, color = color.green)
label_b = label(pos=vec(max(x,max(y,z))+10,5,0), box = 0 ,height = 20, color = color.green)
label_c = label(pos=vec(max(x,max(y,z))+10,0,0), box = 0 ,height = 20, color = color.green)

while True:
    rate(10)
    label_a.text = '{}{:.1f}'.format('a = ',a)
    label_b.text = '{}{:.1f}'.format('b = ',b)
    label_c.text = '{}{:.1f}'.format('c = ',c)
    
    ev = scene.waitfor('click keydown')
    if ev.key == 'A'or ev.key == 'a':a += 0.1
    if ev.key == 'B'or ev.key == 'b':b += 0.1
    if ev.key == 'C'or ev.key == 'c':c += 0.1
    
    if ev.key == 'S'or ev.key == 's':a -= 0.1
    if ev.key == 'N'or ev.key == 'n':b -= 0.1
    if ev.key == 'V'or ev.key == 'v':c -= 0.1
    
    sz = 2 * mag(ball.pos) * dt(plane.up,ball.pos) / (mag(plane.up)*mag(ball.pos))
    n = norm(plane.up) * sz
    
    plane.up = vec(a,b,c)
    normal.axis = n
    
    arr_out.axis = n - ball.pos
```

## 延伸內容

既然有反射勢必有折射定律的模擬，牽扯到不同介質的折射率以及角度的計算$n_1\sin\theta_1 = n_2\sin\theta_2$，之後可以嘗試寫一個模擬試試！