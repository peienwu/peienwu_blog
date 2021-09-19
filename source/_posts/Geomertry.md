---
title: 計算幾何(Computational Geometry)
date: 2021-8-20
tags: 
    - 計算幾何
    - 暑期筆記
categories:
    - C++進階主題
    - 計算幾何
mathjax: true
---

暑假剩沒多少天了，趕緊追進度啊！

## 課程內容

### 座標與向量
* 長度、角度、座標、向量
* 最常以座標、向量表示
<!-- more -->
* Define x first, y second
* 內積Dot：$A \cdot B = A_xB_x+A_yB_y$
* 外積Cross：$A \times B = A_xB_y-A_yB_x$
* 運算子重載：加減乘除、取純量（$abs()$）

### 有向面積
* 用外積算面積（有正負：有向面積）
* 逆時針為正、順時針為負
* 多邊形面積：任選平面上一點A，將所有點與A連線
* 透過順、逆加總有向面積（p0,p1...pn,其中p0=pn）
* AREA = $\frac{1}{2}\sum_{i=0}^{N-1}\vec{P_i}\times\vec{P_{i+1}}$

### 線段相交
* 線段不平行：$P_1$ 與$P_2$ 會在線段$P_3$、$P_4$異側（方向函數）
* 線段平行：檢驗是否共線、並確認某一個點是否在線段上

### 誤差分析：EPS
* 使用二進位儲存：必產生誤差
* 精度：float $10^{-7}$, double $10^{-16}$, long double $10^{-19}$
* 誤差容忍值$eps$，將 $x\pm eps$視為 $x$
* 重載運算子：==（視為相等）,>,<，加上誤差範圍
* Eps大小：多落在 $10^{-6}$到 $10^{-12}$ 之間
* 誤差：加減法，絕對誤差相加；乘除法，相對誤差相加
* 下界：數字範圍為V內時，$eps$ 至少要VK 乘上資料型態本身誤差
* 上界：題目一般會給
* 避免誤差大法：非到最後關頭，否則都用整數運算！

### 三角函數
* 泰勒展開式逼近，時間並非O(1)
* $atan2(y,x) = \theta$，回傳值域$(-pi,pi]$
* 回傳long double ：使用$atan2l(y,x)$
* 常數大，不建議使用

### IEEE 754
* Sign, Exponent, Mantissa
* 正負號、指數部分、小數部分

### 極角排序
* 給定很多點，依照與某特定點（原點）的角度進行排序
* Sort by cross，依照內積排序
* 題目：平面上n個點，問一條直線最多通過幾個點 
 
### 凸包
* 多邊形：簡單多邊形（邊不相交）、凸多邊形（內角都≤180）、凹多邊形（有內角>180）
* 能包住所有點的凸多邊形
* 凸包求法：Monotone Chain（二維平面）、DC（三維）
* **Monotone Chain**
    * 將所有點按照(x,y)排序
    * 把下凸包、上凸包「圍」出來
    * 合併下凸包、上凸包
    * 開一個vector（功能為stack）紀錄當前下半凸包
    * 檢查新加入的點會讓哪些點不再是凸包上的點

### 模擬退火(SA)
* 尋找空間中近似最優解
* 一個隨機算法
* 例題：給你平面上 N 個點，請你找出一個點，使得這個點連到這 N 個點的距離總和最短

## 計算幾何函式模板

計算幾何最重要的莫過於座標上的點，實作方式可以用 $std::pair$ 或是自己定義一個類別，將點的資訊以及相關的運算式定義出來。我們總共需要重載點的大於、等於、小於的運算子，以及加法減法、向量外積內積等，同時還有很多功能是可以繼續定義下去，例如向量乘上一個定值，可以繼續加入類別中。

### 點的模板及運算子重載
```cpp=
struct pt{
    int x,y;
    bool operator < (pt b){
        if(x == b.x)return y < b.y;
        return x < b.x;
    }
    bool operator > (pt b){
        if(x == b.x)return y > b.y;
        return x > b.x;
    }
    bool operator == (pt b){
        if(abs(x-b.x)<=eps && abs(y-b.y)<=eps)return true;
        return false;
    }
    pt operator+(pt b) {return {x + b.x, y + b.y};} //向量相加
    pt operator-(pt b) {return {x - b.x, y - b.y};} //向量相減
    int operator^(pt b) {return x * b.y - y * b.x;} //向量外積cross
    int operator*(pt b) {return x * b.x + y * b.y;} //向量內積dot
};
```
以上是預設點的x座標y座標都是整數的情況，如果要改成使用自定義型別，可以改用樣板(Template)自定義資料型別，根據題目的要求，使用整數或是浮點數進行運算。
```cpp=+
template <typename T>
class pt{
    T x,y;
    //下方運算子重載與上方相同
}
pt<int> p[N];        //點座標宣告為整數
pt<double> pp[N];    //點的不同資料型別宣告
```

### 方向函數
針對 $\vec a$ 以及 $\vec b$ 外積的結果，可以知道兩者之間相對的方向。如果 $\vec a$ 和 $\vec b$ 共線，則回傳0，$\vec a$ 轉向 $\vec b$ 如果是順時針則回傳1，其餘回傳-1。

```cpp=+
int dir(pt a, pt b, pt o) {                         //方向函數
    int cross = (a - o) ^ (b - o);
    if(fabs(cross) <= eps) return 0;
    else if(cross > 0) return 1;
    else return -1;
}
```

### 點與線段關係
以下函式可以判斷點o是否在 $\overline{AB}$ 上，首先利用外積是否為0判斷是 $\overline{OA}$ 與 $\overline{OB}$ 是否平行；接著以內積判斷是否在線段中，而非線段的兩側（平行的條件下內積只可能是1或是-1）。

```cpp=+
bool onseg(pt a, pt b, pt o){         //o是否在ab線段上
    int cross = (a - o) ^ (b - o);    //是否平行
    int dot = (a - o) * (b - o);      //是否在線段中
    return (cross == 0)&&(dot <= 0);
}
```
### 線段相交
以下函式為給定四個點$A,B,C,D$，判斷 $\overline{AB}$ 是否相交於 $\overline{CD}$。首先是特例的判斷，線段的其中一端點在另一線段上，利用上方點與線段關係的函式完成這個判斷。

特例判斷完成之後，我們需要用到上方方向函式判斷線段兩端點是否在另一條線段的異側，即以下的關係式：
$$dir(A,B,C)\times dir(A,B,D) < 0$$

我們要檢查兩條線段，其相乘結果必須皆為負數，表示處於線段的異側！最後是平行線的判斷，如果兩線平行且相交，表示兩線共線，這可以在特例時就被判斷出來。

```cpp=+
bool Intersection(pt a, pt b, pt c, pt d){      //線段ab是否與cd相交
    if(onseg(a,b,c)||onseg(a,b,d))return true;  //點c、d是否洽在線段ab上
    if(onseg(c,d,a)||onseg(c,d,b))return true;  //點a、b是否洽在線段cd上
    if(dir(a,b,c)*dir(a,b,d)==-1 && dir(c,d,a)*dir(c,d,b)==-1)
        return true;                            //對於線段兩端點看另外兩端點必須方向相反
    return false;
}
```
### 極角排序
在進行全點對線段共線問題的判斷時，使用極角排序通常會比單純暴力枚舉更快速。極角也就是極座標中每一個跟原點的夾角。如果兩個點位在左半平與右半平面，則先將其判斷出來，如此才能確定起始的角度為何。

如果位在同一個左右半平面，則透過外積的方式比較兩個向量的先後順序。以下程式碼是從座標平面270度的地方開始逆時針掃一圈依序經過的點。

```cpp=+
bool cmp(pt a, pt b){
    bool f1 = a < pt{0,0};
    bool f2 = b < pt{0,0};
    if(f1 != f2)return f1 < f2;
    return (a ^ b) > 0;
    //逆時針將點進行極角排序，從270度開始逆時針
}
sort(p.begin(),p.end(),cmp);     //以id為原點進行極角排序
```

### 凸包函數（使用Monotone Chain）
首先先將所有點依照x座標進行排序，之後用掃描線由左而右的將符合要求的點推入維護的單調容器中，維護下凸包，接著利用reverse()函數將所有點逆序，也就是x座標由大到小讓掃描線由右而左掃過一遍，將上凸包也圍起來。時間複雜度為$O(n\log n)$。

```cpp=+
bool check(pt a, pt b, pt o){
    pt aa = a - o;
    pt bb = b - o;
    return (aa ^ bb) >= 0;
}

vector<pt> convex_hull(){
    vector<pt> hull;
    sort(p.begin(),p.end(),cmp);       //首先對x進行排序
    for(auto i : p){                   //依序走訪，如果遇到外積<0則不在凸包上
        while(hull.size() > 1 && check(i,hull[hull.size()-1],hull[hull.size()-2])){
            hull.pop_back();
        }
        hull.push_back(i);             //在凸包hull的每一點都符合外積小於0
    }
    int down_hull = hull.size();
    hull.pop_back();                   //x最大的點會在凸包上，不用做兩次先pop一次
    reverse(p.begin(),p.end());        //將所有點逆序之後做一次上面的凸包
    for(auto i: p){
        while(hull.size() > down_hull && check(i,hull[hull.size()-1],hull[hull.size()-2])){
            hull.pop_back();
        }
        hull.push_back(i);
    }
    return hull;                       //起點會經過兩次，剛好來算有向面積
}
```

### 旋轉卡尺
旋轉卡尺可以被應用在尋找最遠點對、面積最大三角形等問題。利用兩條平行的線中間夾著凸包，繞一圈的過程中更新需要求的數值。實作上來說就是使用兩個指針，分別指向旋轉卡尺的平行線所在的兩個點，依照旋轉的方向進行增減的動作！

```cpp=+
bool check2(pt a,pt b,pt c,pt d){
    int aa = abs((a - c)^(b - c));
    int bb = abs((a - d)^(b - d));
    return aa < bb;
}

void solve(){
    int ans = 0,d = h,sz = hull.size();
    rep(i,0,sz-1){
        while(check2(hull[i],hull[(i+1)%sz],hull[d],hull[(d+1)%sz]))
            d = (d+1)%sz;
        ans = max(ans,(hull[i]-hull[d]).dis());
        ans = max(ans,(hull[(i+1)%sz]-hull[d]).dis());
    }
}
```

## 基本數學知識

計算幾何圍繞著幾個主軸，向量運算、內積、外積，利用它們進行角度、共線與否、距離等等的判斷。之前有稍微接觸過向量，不過內積與外積是第一次碰到的主題。

因為目前絕大部分的討論都是在二維平面上進行，因此以下都是以二維平面為前提所進行的討論！

### 內積（點積）
內積跟 $\cos\theta$ 有關，因此主要可以幫助我們判斷線段是否垂直（等於零）、以及共線時點位於正向或是反向的判斷（等於正負一）。兩個向量 $\vec u$ 以及 $\vec v$ 的內積可以寫成以下關係式：

$$\vec u\cdot \vec v = |\vec u||\vec v|\cos\theta = u_1v_1 + u_2v_2$$

兩個向量做內積的正負號會跟餘弦函數的正負變化相同（如下圖），值域為 $[-|\vec u||\vec v|,|\vec u||\vec v|]$，並且在 $\theta = 0,\pi$ 有最大最小值，$\theta = \frac{\pi}{2},\frac{3\pi}{2}$ 的值為零。

![](https://i.imgur.com/Si0hlNr.png)


### 外積（叉積）
外積跟 $\sin\theta$ 有關，主要可以判斷兩向量方向關係（順逆時針旋轉）、是否平行、比較角度大小等。外積的應用十分廣泛，找凸包以及旋轉卡尺都會用到外積判斷兩個向量角度關係。兩個向量 $\vec u$ 以及 $\vec v$ 的外積可以寫成以下關係式：

$$\vec u\times \vec v = |\vec u||\vec v|\sin\theta = \begin{bmatrix}u_1&u_2\\v_1&v_2\end{bmatrix}=u_1v_2 - u_2v_1$$

用更簡單的方式理解外積 $\vec u\times \vec v$ ，其正負值可以想像成 $\vec u$ 轉向 $\vec v$ 所經的劣弧順逆時鐘方向。順時針為正、逆時針為負。

關於外積的數值變化，與正弦函數的變化是一樣的（下圖），其值域跟內積一樣，不過最大最小值發生在 $\theta = \frac{\pi}{2},\frac{3\pi}{2}$，並在 $\theta = 0,\pi$ 時兩向量叉積為零。

![](https://i.imgur.com/ozKfaKL.png)


### 面積
#### 測量師公式（行列式）

這是一個從給定多邊形的座標推得面積的公式，寫成很多個三角形有向面積的總和。以下圖來說，$\triangle FBC$、$\triangle FCD$ 、$\triangle FDE$ 的有向面積皆大於零，而 $\triangle FAB$、$\triangle FEA$ 都會因為有向面積是負的（逆時針旋轉）而被扣除掉，運算的總和即是多邊形 $ABCDE$ 的面積！

![](https://i.imgur.com/did2OzY.png)

一般化的公式，多邊形上總共有 $N$ 個點，令第 $N+1$ 個點為第1個點（為了要繞一圈計算面積），多邊形面積為：
$$AREA = \frac{1}{2}\sum_{i=1}^{N}\vec{P_i}\times\vec{P_{i+1}}$$


#### 三角形外積面積公式
三角形面積有非常多算法，不過利用外積的公式還是第一次聽到。以下是公式推導過程：

先從高中三角函數的三角形公式開始：
$$\begin{split}\triangle ABC &= \frac{1}{2}\overline{AB}\,\overline{AC}\cdot\sin A
\\&=\frac{1}{2}\sqrt{\overline{AB}^2\,\overline{AC}^2\,(1-\cos^2A)}
\\&=\frac{1}{2}\sqrt{\overline{AB}^2\,\overline{AC}^2-(\overline{AB}\cdot\overline{AC})^2}
\\&=\frac{1}{2}\sqrt{(x_1^2+y_1^2)(x_2^2+y_2^2)-(x_1\,x_2+y_1\,y_2)^2}
\\&=\frac{1}{2}\sqrt{(x_1^2\,y_2^2)+(x_2^2\,y_1^2)-2x_1\,y_2\,x_2\,y_1}
\\&=\frac{1}{2}\sqrt{[(x_1\,y_2)-(x_2\,y_1)]^2}
\\&=\frac{1}{2}|\overrightarrow{AB}\times \overrightarrow{AC}|\end{split}$$

將三角形其中一點對另外兩點的向量做外積，除以2即為三角形面積。這個公式會在旋轉卡尺的地方使用到！

#### 平行四邊形面積
根據上面三角形面積公式的推導，可以相對應得知道兩向量所夾平行四邊形面積公式：
$$AREA = |\overrightarrow{AB}\times \overrightarrow{AC}|$$

![](https://i.imgur.com/9mUdyV6.png)
