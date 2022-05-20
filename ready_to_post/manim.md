---
title: Manim學習筆記
date: 2022-2-21
tags: 
    - 數學
    - Manim
categories: 
    - 程式模擬
mathjax: true
---

## Manim安裝

> Manim is an animation engine for explanatory math videos.

利用Manim可以生成許多數學類的動畫影片、以及製作一些動畫模擬！

我是利用Mac安裝Manim，只要按照[這一篇](https://docs.manim.community/en/stable/installation/macos.html)的說明一步步完成即可，不要上網找教學影片，因為很多都是過時的，在安裝上會出現一些問題。

至於使用教學的部分，可以參考這一篇[官方使用手冊](https://docs.manim.community/en/stable/index.html)。Manim總共有三種版本，建議使用Manim Community比較方便，資源比較多！

<!--more-->

## 語法筆記

### 架構程式碼

```python=
from manim import *

class Test(Scene):
    def construct(self):
        #程式碼區塊
```

### 終端指令

* **mp4**:    ```manim test.py Test -pqm```
* **gif**: ```manim test.py Test -pqm -i```

影片品質設定：```-pql,-pqm,-pqh```

### Updates

```python=
from manim import *

class Test(Scene):
    def construct(self):

        rec = Rectangle(color = WHITE,width = 2,height = 2.5).to_edge(UL)
        circ = Circle().to_edge(DOWN)
        arr = always_redraw(
            lambda: Line(
                start = rec.get_bottom(),end = circ.get_top(),buff = 0.5
            ).add_tip()
        )

        self.play(Create(VGroup(rec,circ,arr)))
        self.wait()
        self.play(rec.animate.to_edge(UR),circ.animate.scale(0.5),run_time = 3)
```

### Value Tracker

```python=
from manim import *

class Updaters(Scene):
    def construct(self):
        
        k = ValueTracker(0)
        num = always_redraw(
            lambda: DecimalNumber().set_value(k.get_value())
        )

        self.play(FadeIn(num))
        self.play(k.animate.set_value(10),run_time = 5,rate_func = smooth)
        self.wait()
```

### NumberPlane

注意：```get_graph() —> plot()```
自動排版：```alt + shift + f```

```python=
from manim import *
import math


class Test(Scene):
    def construct(self):

        plane = (
            NumberPlane(x_range=[-20, 20, 5], x_length=8,
                        y_range=[-4, 20, 5], y_length=8)
            .to_edge(DOWN)
            .add_coordinates()
        )
        l = plane.get_axis_labels(x_label='x', y_label='f(x)')

        par = plane.plot(lambda x: (0.5*x) ** 2,
                         x_range=[-10, 10], color=GREEN)

        func_l = MathTex("f(x) = {x}^2").next_to(par, RIGHT).set_color(GREEN)

        area = plane.get_riemann_rectangles(
            graph=par, x_range=[-5, 5], dx=1, stroke_width=0.1, stroke_color=WHITE)

        self.play(DrawBorderThenFill(plane), run_time=2)
        self.play(Create(VGroup(par, l, func_l)), run_time=2)
        self.wait()
        self.play(Create(area))
        self.wait()
```

### ValueTrackers to Graphing Scenes

```python=
from manim import *


class Test(Scene):
    def construct(self):

        k = ValueTracker(-4)
        ax = Axes(x_range=[-4, 4, 1], y_range=[-2, 16, 2],
                  x_length=5, y_length=5).to_edge(DOWN).add_coordinates()
        func = ax.plot(lambda x: x ** 2, x_range=[-4, 4], color=BLUE)
        slope = always_redraw(
            lambda: ax.get_secant_slope_group(
                graph=func, dx=0.01, x=k.get_value(), secant_line_color=GREEN, secant_line_length=5)
        )

        pt = always_redraw(
            lambda:
            Dot().move_to(ax.c2p(k.get_value(), func.underlying_function(k.get_value())))
        )

        self.add(ax, func, slope, pt)
        self.wait()
        self.play(k.animate.set_value(4), run_time=10)
        self.wait()
```

### Open a file

**1. svg file:**

```python=
from manim import *

H = "/Users/peienwu/Downloads/"

class Test(Scene):
    def construct(self):
        Icon = SVGMobject(f"{H}//test.svg")

        self.play(FadeIn(Icon))
        self.wait()
```

**2. png file:**

```python=
from manim import *


class Test(Scene):
    def construct(self):
        Icon = ImageMobject(f"/Users/peienwu/Downloads/minion.jpeg")

        self.play(FadeIn(Icon))
        self.wait()
        self.play(Icon.animate.to_edge(LEFT))
```

### Bar chart

```python=
from pyclbr import Function
from manim import *


class DifferentRotations(Scene):
    def construct(self):
        num = [1, 2, 5, 4, 3]
        ver = ["v1.0", "v2.0", "v3.0", "v4.0", "v5.0"]

        r = BarChart(
            num,
            max_value=max(num),
            bar_names=ver,

        )
        self.play(Create(r), run_time=2)
        self.wait()
```

## 影片成果

* 文章：[Bertrand Paradox](https://peienwu.com/2022/02/07/Bertrand_Paradox/#more)
* 文章：[橢圓軌道上行星到太陽平均距離](https://peienwu.com/2022/04/21/%E6%A9%A2%E5%9C%93%E8%BB%8C%E9%81%93%E4%B8%8A%E8%A1%8C%E6%98%9F%E5%88%B0%E5%A4%AA%E9%99%BD%E5%B9%B3%E5%9D%87%E8%B7%9D%E9%9B%A2/)
* 影片：[Youtube頻道](https://www.youtube.com/channel/UC9ioraLAwW20eSupf2-e6Dw)
