---
title: Geometry in Leetcode & CSES
date: 2021-12-20
tags: 
    - 計算幾何
categories:
    - C++進階主題
    - 計算幾何
mathjax: true
---
## CSES Geometry

- Point Location Test
- Line Segment Intersection
- Polygon Area
- Point in Polygon
- Polygon Lattice Points
- Minimum Euclidean Distance
- Convex Hull

<!--more-->

### Point Location Test
[CODE](https://cses.fi/paste/3a495a0f4bdec8af2df7b4/)

給你三個點 $P_1,P_2,P_3$ ，判斷出向量 $(P_1,P_2)$ 之於 $P_3$ 的方向為何？
關係共有相交、位於左側以及位於右側。

### Line Segment Intersection
[CODE](https://cses.fi/paste/854258b8f67b255c2df828/)

給你兩條線段，判斷他們是否相交。線段相交裸題。


### Polygon Area
[CODE](https://cses.fi/paste/49f2a020a47797b42df861/)

給你N個點，計算出此多邊形圍成的面積為何。帶入行列式公式可解。

### Point in Polygon
[CODE](https://cses.fi/paste/d0edfaf7380124852e075e/)

給你N個點構成的一個簡單多邊形，判斷一個點是否在多邊形內部。

參考資料：[greekforgreek](https://www.geeksforgeeks.org/how-to-check-if-a-given-point-lies-inside-a-polygon/)

![](https://i.imgur.com/q3bqqWZ.png)

凸多邊形的情況，我們可以利用內角和是360度轉一圈的的方法，判斷一點是否在多邊形內部。但本題是簡單多邊形，因此用這種方法是不可行的。

![](https://i.imgur.com/FbcWiwl.png)

這張圖是作法，我們做出一個由x點出發向x軸正向的射線，計算這一條射線跟多邊形共有幾個交點。如果是奇數，表示在內部；反之則是在外部。交在多邊形上的點要特別注意，因為他可能會被統計到兩次，因此我們特別處理當點相交一點時，只計算那條邊的兩端點中，y座標比較大的那一點是否相交的情況。

最後，我們發現遇上如圖中g點的狀況直接就被處理好了！他會被算到零次，因為在看兩條邊加上去之後，又因為頂點是y座標比較大的，會被剪掉兩次。


### Polygon Lattice Points

[CODE](https://cses.fi/paste/7fa78ccd60f7877f2e07dc/)

給定頂點座標均是整點（或正方形格子點）的簡單多邊形，皮克定理說明了其面積 $A$ 和內部格點數目 $i$、邊上格點數目 $b$ 的關係：
$$A = i + \frac{b}{2} - 1$$

[PROOF](http://episte.math.ntu.edu.tw/articles/sm/sm_25_10_1/index.html)

### Minimum Euclidean Distance

[CODE](https://cses.fi/paste/a728ec171111ce792e091f/)

[最近點對問題](https://hackmd.io/@peienwu/closest_pair)。不過，既然是計算幾何，我們就用掃描線做最近點對。掃描線有兩個做法（可以參考那個連結），至於搭配set輔助步驟就是：

1. 將點輸入並且排序，X座標為主，Y座標為輔。
2. 使用set，並以Y座標為排序基準（pair的首項），以儲存第 $i$ 點的左方、水平距離小於等於d的點。
3. 右掃描線依序窮舉各點作為右端點。
　(1) Erase與右端點水平距離大於d的點們（左掃描線右移）
　(2) 用二分搜找出與第 $i$ 點垂直距離小於d的點，並嘗試更新
　(3) 將第 $i$ 點加入set中。
 
![](https://i.imgur.com/yMs369S.png)



### Convex Hull

[CODE](https://cses.fi/paste/ac97fa8545e467692e09aa/)

凸包裸題。

## Leetcode：Geography

Leetcode 總共有27題的tag是計算幾何的，[題單在這裡](https://leetcode.com/tag/geometry/)。有三題被鎖起來不能看，所以總共有24題。

### 149 Max Points on a Line

[題目連結](https://leetcode.com/problems/max-points-on-a-line)

```cpp=
class Solution {
public:
    
    int gcd(int a,int b){
        if(a > b)swap(a,b);
        if(a == 0)return b;
        return gcd(b % a, a);
    }
    
    int maxPoints(vector<vector<int>>& points) {
        int n = points.size(),ans = 0;
        for(int i = 0;i < n;i++){
            map<pair<int,int>,int> mp;
            int same = 0,hori = 0;
            for(int j = i+1;j < n;j++){
                int dx = points[i][0] - points[j][0];
                int dy = points[i][1] - points[j][1];
                if(dx == 0 && dy == 0){same++;continue;}
                if(dx == 0 && dy != 0){hori++;continue;}
                int g = gcd(abs(dx),abs(dy));
                if(dy < 0 || (dy == 0 && dx < 0)){dx = -dx;dy = -dy;}
                mp[{dx/g,dy/g}]++;
            }
            int sum = hori + same + 1;
            for(auto it : mp)sum = max(sum,it.second + 1);
            ans = max(ans,sum);
        }
        return ans;
    }
};
```


### 223 Rectangle Area

[題目連結](https://leetcode.com/problems/rectangle-area)

```cpp=
class Solution {
public:
    int computeArea(int ax1, int ay1, int ax2, int ay2, int bx1, int by1, int bx2, int by2) {
        int x = max(min(ax2,bx2) - max(ax1,bx1),0);
        int y = max(min(ay2,by2) - max(ay1,by1),0);
        int ans = (ax2 - ax1)*(ay2 - ay1)+(bx2 - bx1)*(by2 - by1)-x * y;
        return ans;
    }
};
```

### 335 Self Crossing

[題目連結](https://leetcode.com/problems/self-crossing)

```cpp=
class Solution {
public:
    bool isSelfCrossing(vector<int>& d) {
        int n = d.size();
        if(n <= 3)return false;
        for(int i = 3;i < n;i++){
            //第四條、第五條、第六條交第一條
            if(d[i] >= d[i-2] && d[i-1] <= d[i-3])return true;
            if(i >= 4 && d[i-1] == d[i-3] && d[i] + d[i-4] >= d[i-2])return true;
            if(i >= 5 && d[i-2] >= d[i-4] && d[i] >= d[i-2] - d[i-4]
              && d[i-1] >= d[i-3]-d[i-5] && d[i-1] <= d[i-3])return true;
            
        }
        return false;
    }
};
```


### 587 Erect the Fence

[題目連結](https://leetcode.com/problems/erect-the-fence)

```cpp=
#define pii pair<int,int>
#define ff first
#define ss second

class Solution {
public:
    
    bool check(pii a,pii b,pii o){
        pii aa = {a.ff - o.ff,a.ss - o.ss};
        pii bb = {b.ff - o.ff,b.ss - o.ss};
        int cross = aa.ff * bb.ss - aa.ss * bb.ff;
        return cross > 0;
    }
    
    vector<vector<int>> outerTrees(vector<vector<int>>& point) {
        vector<pii> h;
        int n = point.size();
        vector<pii> p(n);
        for(int i = 0;i < n;i++)p[i] = {point[i][0],point[i][1]};
        sort(p.begin(),p.end());
        for(auto i : p){
            while(h.size() > 1 && check(i,h[h.size()-1],h[h.size()-2]))
                h.pop_back();
            h.push_back(i);
        }
        int down = h.size();
        h.pop_back();
        reverse(p.begin(),p.end());
        for(auto i : p){
            while(h.size() > down && check(i,h[h.size()-1],h[h.size()-2]))
                h.pop_back();
            h.push_back(i);
        }
        set<pii> s;for(auto i : h)s.insert(i);
        n = s.size();
        vector<vector<int>> ans;ans.resize(n);
        for(int i=0;i<n;i++)ans[i].resize(2);
        int id = 0;
        for(auto i : s){ans[id][0] = i.ff;ans[id][1] = i.ss;id++;}
        return ans;
    }
};
```


### 593 Valid Square

[題目連結](https://leetcode.com/problems/valid-square)

```cpp=
class Solution {
public:
    
    int dis(vector<int>& p1, vector<int>& p2){
        int x = p1[0] - p2[0],y = p1[1] - p2[1];
        return x * x + y * y;
    }
    
    bool validSquare(vector<int>& p1, vector<int>& p2, vector<int>& p3, vector<int>& p4) {
        map<int,int>mp;     //長度、個數
        mp[dis(p1,p2)]++;
        mp[dis(p1,p3)]++;
        mp[dis(p1,p4)]++;
        mp[dis(p2,p3)]++;
        mp[dis(p2,p4)]++;
        mp[dis(p3,p4)]++;
        return mp.size() == 2 && mp.begin()->second == 4;
    }
};
```

### 812	Largest Triangle Area

[題目連結](https://leetcode.com/problems/largest-triangle-area)
```cpp=
class Solution {
public:
    
    int cross(int x1,int y1,int x2,int y2){
        return x1 * y2 - x2 * y1;
    }
    
    double area(int a,int b,int c,int d,int e,int f){
        double sum = 0;
        sum += cross(a,b,c,d);
        sum += cross(c,d,e,f);
        sum += cross(e,f,a,b);
        return abs(sum / 2.0);
    }
    
    double largestTriangleArea(vector<vector<int>>& points) {
        int n = points.size();
        double ans = 0;
        for(int i = 0;i < n;i++){
            for(int j = i + 1;j < n;j++){
                for(int p = j + 1;p < n;p++){
                    ans = max(ans,area(points[i][0],points[i][1]
                                      ,points[j][0],points[j][1]
                                      ,points[p][0],points[p][1]));
                }
            }
        }
        return ans;
    }
};
```


### 836 Rectangle Overlap

[題目連結](https://leetcode.com/problems/rectangle-overlap)

```cpp=
class Solution {
public:
    bool isRectangleOverlap(vector<int>& rec1, vector<int>& rec2) {
        //最小的右端點 - 最大的左端點
        int x = min(rec1[2],rec2[2]) - max(rec1[0],rec2[0]);
        //最小的上端點 - 最大的下端點
        int y = min(rec1[3],rec2[3]) - max(rec1[1],rec2[1]);
        return x > 0 && y > 0;
    }
};
```


### 858 Mirror Reflection

[題目連結](https://leetcode.com/problems/mirror-reflection)

```cpp=
class Solution {
public:
    int gcd(int a,int b){
        if(a == 0)return b;
        return gcd(b % a,a);
    }
    
    int mirrorReflection(int p, int q) {
        int len = p * q / gcd(q,p);
        int a = (len / p) % 2,b = (len / q) % 2;
        if(a == 0 && b == 1)return 0;
        else if(a == 1 && b == 1)return 1;
        else return 2;
        
    }
};
```


### 478 Generate Random Point in a Circle

[題目連結](https://leetcode.com/problems/generate-random-point-in-a-circle)

直接Random半徑會出事（可能不夠亂，或是半徑太小），如果random面積之後算半徑才OK。

```cpp=
class Solution {
public:
    double R,X,Y;
    Solution(double radius, double x_center, double y_center) {
        R = radius;X = x_center;Y = y_center;
        srand(time(NULL));
    }
    
    vector<double> randPoint() {
        double Area = rand() * R * R * M_PI / (RAND_MAX + 1.0);
        double r = sqrt(Area / M_PI);
        double theta = 2.0 * M_PI * rand() / (RAND_MAX + 1.0);
        vector<double> ans;
        ans.push_back(X + r * cos(theta));
        ans.push_back(Y + r * sin(theta));
        return ans;
    }
};

/**
 * Your Solution object will be instantiated and called as such:
 * Solution* obj = new Solution(radius, x_center, y_center);
 * vector<double> param_1 = obj->randPoint();
 */
```


### 883 Projection Area of 3D Shapes

[題目連結](https://leetcode.com/problems/projection-area-of-3d-shapes)

三種不同的投影對應到三種不同的角度看圖形。x-y的面積即為由上而下看有方格的個數。x-z是從前方看，因此對應到的是每一行的最大方塊個數。

```cpp=
class Solution {
public:
    int projectionArea(vector<vector<int>>& grid) {
        int n = grid.size(),ans = 0;
        for(int i = 0;i < n;i++){
            int maxR = 0,maxC = 0;
            for(int j = 0;j < n;j++){
                if(grid[i][j] > 0)ans++;        //由上往下看
                maxR = max(maxR,grid[i][j]);    //由側邊看
                maxC = max(maxC,grid[j][i]);    //由前面看
            }
            ans += maxR + maxC;
        }
        return ans;
    }
};
```


### 892 Surface Area of 3D Shapes

[題目連結](https://leetcode.com/problems/surface-area-of-3d-shapes)

```cpp=
class Solution {
public:
    int surfaceArea(vector<vector<int>>& grid) {
        int ans = 0,n = grid.size();
        for(int i = 0;i < n;i++){
            for(int j = 0;j < n;j++){
                if(grid[i][j] > 0)ans += 4 * grid[i][j] + 2;
                if(i < n - 1){
                    ans -= min(grid[i][j],grid[i+1][j])*2;
                }
                if(j < n - 1){
                    ans -= min(grid[i][j],grid[i][j+1])*2;
                }
            }
        }
        return ans;
    }
};
```


### 939 Minimum Area Rectangle

[題目連結](https://leetcode.com/problems/minimum-area-rectangle)
```cpp=
class Solution {
public:
    int minAreaRect(vector<vector<int>>& points) {
        vector<vector<int>>p = points;
        int n = points.size();
        set<pair<int,int>>s;
        for(int i=0;i<n;i++)s.insert({p[i][0],p[i][1]});
        int ans = INT_MAX;
        for(int i = 0;i < n;i++){
            for(int j = i+1;j < n;j++){
                if(s.find({p[i][0],p[j][1]})!=s.end() 
                   && s.find({p[j][0],p[i][1]})!=s.end()){
                    if(p[i][0] == p[j][0] || p[i][1] == p[j][1])continue;
                    ans = min(ans,abs(p[i][0]-p[j][0])*abs(p[i][1]-p[j][1]));
                }
            }
        }
        if(ans == INT_MAX)return 0;
        else return ans;
    }
};
```


### 963 Minimum Area Rectangle II

[題目連結](https://leetcode.com/problems/minimum-area-rectangle-ii)

```cpp=
class Solution {
public:
    double minAreaFreeRect(vector<vector<int>>& p){
        set<pair<int,int>> s;
        int n = p.size();
        for(int i = 0;i < n;i++)s.insert({p[i][0],p[i][1]});
        double ans = 1e9;
        for(int i = 0;i < n;i++){
            for(int j = i+1;j < n;j++){
                int x1 = p[j][0] - p[i][0];
                int y1 = p[j][1] - p[i][1];
                for(int k = j+1;k < n;k++){
                    int x2 = p[k][0] - p[i][0];
                    int y2 = p[k][1] - p[i][1];
                    if(x1 * x2 + y1 * y2 != 0)continue;
                    int nx = p[k][0] + x1,ny = p[k][1] + y1;
                    if(s.find({nx,ny}) != s.end()){
                        ans = min(ans,sqrt(x1*x1+y1*y1) * sqrt(x2*x2+y2*y2));
                    }
                }
            }
        }
        if(ans == 1e9)return 0;
        return ans;
    }
};
```


### 973 K Closest Points to Origin

[題目連結](https://leetcode.com/problems/k-closest-points-to-origin)

```cpp=
class Solution {
public:
    vector<vector<int>> kClosest(vector<vector<int>>& points, int k) {
        int n = points.size();
        multimap<int,int> mp;
        for(int i = 0;i < n;i++){
            int x = points[i][0];
            int y = points[i][1];
            mp.insert({x * x + y * y,i});
        }
        auto it = mp.begin();
        vector<vector<int>> ans;
        for(int i=0;i<k;i++){
            int id = it->second;
            ans.push_back(points[id]);
            it++;
        }
        return ans;
    }
};
```


### 1030 Matrix Cells in Distance Order

[題目連結](https://leetcode.com/problems/matrix-cells-in-distance-order)

```cpp=
class Solution {
public:
    vector<vector<int>> allCellsDistOrder(int rows, int cols, int rCenter, int cCenter) {
        int n = rows,m = cols;
        multimap<int,pair<int,int>> mp;
        for(int i = 0;i < n;i++){
            for(int j = 0;j < m;j++){
                int dis = abs(i - rCenter) + abs(j - cCenter);
                mp.insert({dis,{i,j}});
            }
        }
        vector<vector<int>> ans;ans.resize(n*m);
        for(int i=0;i<n*m;i++)ans[i].resize(2,0);
        int id = 0;
        for(auto it : mp){
            ans[id][0] = (it.second.first);
            ans[id][1] = (it.second.second);
            id++;
        }
        return ans;
    }
};
```


### 1037 Valid Boomerang

[題目連結](https://leetcode.com/problems/valid-boomerang)

```cpp=
class Solution {
public:
    bool isBoomerang(vector<vector<int>>& points) {
        int x1 = points[1][0]-points[0][0],y1 = points[1][1]-points[0][1];
        int x2 = points[2][0]-points[1][0],y2 = points[2][1]-points[1][1];
        int cross = x1 * y2 - x2 * y1;
        if(cross == 0)return false;
        return true;
    }
};
```

### 1232 Check If It Is a Straight Line

[題目連結](https://leetcode.com/problems/check-if-it-is-a-straight-line)

```cpp=
class Solution {
public:
    bool checkStraightLine(vector<vector<int>>& coordinates) {
        vector<vector<int>> p = coordinates;
        int n = p.size();
        for(int i = 0;i < n-2;i++){
            int x1 = p[i+1][0] - p[i][0],y1 = p[i+1][1] - p[i][1];
            int x2 = p[i+2][0] - p[i+1][0],y2 = p[i+2][1] - p[i+1][1];
            if(x1 * y2 != x2 * y1)return false;
        }
        return true;
    }
};
```


### 1266 Minimum Time Visiting All Points

[題目連結](https://leetcode.com/problems/minimum-time-visiting-all-points)

```cpp=
class Solution {
public:
    int minTimeToVisitAllPoints(vector<vector<int>>& points) {
        int n = points.size(),ans = 0;
        vector<vector<int>> p = points;
        for(int i = 0;i < n-1;i++){
            ans += max(abs(p[i+1][0] - p[i][0]),abs(p[i+1][1] - p[i][1]));
        }
        return ans;
    }
};
```


### 1401 Circle and Rectangle Overlapping

[題目連結](https://leetcode.com/problems/circle-and-rectangle-overlapping)

```cpp=
class Solution {
public:
    bool checkOverlap(int radius, int x_center, int y_center, int x1, int y1, int x2, int y2) {
        int x = clamp(x_center,x1,x2) - x_center;
        int y = clamp(y_center,y1,y2) - y_center;
        return x*x + y*y <= radius * radius;
    }
};
```


### 1453 Maximum Number of Darts Inside of a Circular Dartboard

[題目連結](https://leetcode.com/problems/maximum-number-of-darts-inside-of-a-circular-dartboard)

```cpp=
#define ld long double
#define pdd pair<ld,ld>
#define x first
#define y second
#define exp 1e-6

class Solution {
public:
    
    double dis(pdd a,pdd b){
        ld x = a.x - b.x,y = a.y - b.y;
        return sqrt(x * x + y * y);
    }
    
    pair<pdd,pdd> get_center(pdd a,pdd b,ld R){
        pdd mid = {(a.x + b.x) / 2,(a.y + b.y) / 2};
        ld theta = atan2(a.y - b.y, b.x - a.x);
        ld tmp = dis(a,b) / 2, d = sqrt(R * R - tmp * tmp);
        
        pair<pdd,pdd> ans;
        ans.x = {mid.x - d * sin(theta),mid.y - d * cos(theta)};
        ans.y = {mid.x + d * sin(theta),mid.y + d * cos(theta)};
        return ans;
    }
    
    int numPoints(vector<vector<int>>& point, int R) {
        int n = point.size(),ans = 1;
        pdd p[n];for(int i=0;i<n;i++){p[i] = {point[i][0],point[i][1]};}
        for(int i = 0;i < n;i++){
            for(int j = i+1;j < n;j++){
                if(dis(p[i],p[j]) - 2.0 * R >= exp)continue;
                pair<pdd,pdd> cur = get_center(p[i],p[j],R);
                int cnt = 0;
                for(int k = 0;k < n;k++)
                    if(dis(p[k], cur.x) - R<= exp)cnt ++;
                ans = max(ans, cnt);cnt = 0;
                for(int k = 0;k < n;k++)
                    if(dis(p[k], cur.y) - R <= exp)cnt ++;
                ans = max(ans, cnt);
            }
        }
        return ans;
    }
};
```


### 1515 Best Position for a Service Centre

[題目連結](https://leetcode.com/problems/best-position-for-a-service-centre)

這一題是模擬退火，非常酷，之前沒有寫過的東西。

```cpp=
#define eps 1e-6
#define ld long double
#define pdd pair<ld,ld>
#define x first
#define y second

class Solution {
public:
    pdd p[105];int n;
    
    ld dis_all(pdd mid){
        ld sum;
        for(int i = 0;i < n;i++){
            ld x = p[i].x - mid.x,y = p[i].y - mid.y;
            sum += sqrt(x * x + y * y);
        }
        return sum;
    }
    
    double getMinDistSum(vector<vector<int>>& pos) {
        n = pos.size();
        for(int i = 0;i < n;i++)p[i] = {pos[i][0],pos[i][1]};
        pdd cur = p[0];ld mid_dis = dis_all(p[0]);
        ld test_size = 100;
        ld dx[4] = {1.0,0.0,-1.0,0.0},dy[4] = {0.0,1.0,0.0,-1.0};
        bool flag = 0;
        while(test_size > eps){
            flag = 0;
            for(int i = 0;i < 4;i++){
                pdd newp = cur;
                newp.x += dx[i] * test_size;
                newp.y += dy[i] * test_size;
                ld new_dis = dis_all(newp);
                if(new_dis < mid_dis){
                    mid_dis = new_dis;
                    cur = newp;
                    flag = 1;
                    break;
                }
            }
            if(flag == 0)test_size /= 2.0;
        }
        return mid_dis;
    }
};
```

換另外一種迭代方式

```cpp=
#define eps 1e-8
#define ld long double
#define pdd pair<ld,ld>
#define x first
#define y second

class Solution {
public:
    pdd p[105];int n;
    
    ld dis_all(pdd mid){
        ld sum;
        for(int i = 0;i < n;i++){
            ld x = p[i].x - mid.x,y = p[i].y - mid.y;
            sum += sqrt(x * x + y * y);
        }
        return sum;
    }
    
    double getMinDistSum(vector<vector<int>>& pos) {
        srand(time(NULL));
        n = pos.size();
        for(int i = 0;i < n;i++)p[i] = {pos[i][0],pos[i][1]};
        pdd cur = p[0];ld mid_dis = dis_all(p[0]);
        ld test_size = 150.0;
        while(test_size > eps){
            pdd newp = cur;
            int temp = rand();
            newp.x += cos(temp) * test_size;
            newp.y += sin(temp) * test_size;
            ld new_dis = dis_all(newp);
            if(new_dis < mid_dis){
                mid_dis = new_dis;
                cur = newp;
            }
            else test_size *= 0.99;
        }
        return mid_dis;
    }
};
```


### 1610 Maximum Number of Visible Points

[題目連結](https://leetcode.com/problems/maximum-number-of-visible-points)

```cpp=
#define ld long double
const int N = 1e5+5;

class Solution {
public:
    int visiblePoints(vector<vector<int>>& points, int angle, vector<int>& loc) {
        vector<ld> ang;
        int overlap = 0,n = points.size(),ans = 0;
        for(auto p : points){
            if(p[0] == loc[0] && p[1] == loc[1])overlap++;
            else ang.push_back(atan2l(p[1]-loc[1],p[0]-loc[0]) * 180 / (ld)M_PI);
        }
        int sz = ang.size();
        for(int i = 0;i < sz;i++)ang.push_back(ang[i] + 360);
        sort(ang.begin(),ang.end());
        sz = ang.size();
        for(int i = 0,it2 = 0;i < sz;i++){
            while(it2 < sz && ang[it2] - ang[i] <= angle)it2++;
            ans = max(ans,it2 - i);
        }
        return ans + overlap;
    }
};
```

### 1828 Queries on Number of Points Inside a Circle

[題目連結](https://leetcode.com/problems/queries-on-number-of-points-inside-a-circle)

```cpp=
class Solution {
public:
    vector<int> countPoints(vector<vector<int>>& points, vector<vector<int>>& queries) {
        int n = queries.size(),m = points.size();
        vector<int> ans;ans.resize(n);
        for(int i = 0;i < n;i++){
            int rx = queries[i][0],ry = queries[i][1],sum = 0;
            for(int j = 0;j < m;j++){
                int x = points[j][0] - rx,y = points[j][1] - ry;
                if(x * x + y * y <= queries[i][2] * queries[i][2])sum++;
            }
            ans[i] = sum;
        }
        return ans;
    }
};
```

### 2101. Detonate the Maximum Bombs
[題目連結](https://leetcode.com/problems/detonate-the-maximum-bombs/)

把圖形考慮成一張圖，符合條件的就增加一條有向邊，接著對每一個點做一次DFS即可。時間 $O(n^2)$。

```cpp=
#define pii pair<int,int>
#define ld long double

class Solution {
public:
    
    vector<int> E[105];
    ld dis(pii a,pii b){
        int x = a.first - b.first,y = a.second - b.second;
        return sqrt((long long)x * x + (long long)y * y);
    }
    int sum = 1;
    bool vis[105];
    void dfs(int now){
        vis[now] = 1;
        for(auto i : E[now]){
            if(vis[i])continue;
            sum++;vis[i] = 1;
            dfs(i);
        }
    }
    
    int maximumDetonation(vector<vector<int>>& bomb) {
        int n = bomb.size(),ans = 0;
        for(int i = 0;i < n;i++){
            for(int j = 0;j < n;j++){
                pii a = {bomb[i][0],bomb[i][1]},b = {bomb[j][0],bomb[j][1]};
                int r1 = bomb[i][2],r2 = bomb[j][2];
                ld d = dis(a,b);
                if(r1 >= d)E[i].push_back(j);
                if(r2 >= d)E[j].push_back(i);
            }
        }
        for(int i = 0;i < n;i++){
            fill(vis,vis+105,0);
            sum = 1;
            dfs(i);
            ans = max(ans,sum);
        }
        return ans;
    }
};
```


## 延伸問題
- 給定平面上N個點，問一條直線最多能穿越幾個點
- 線段相交座標
- 給你一個線段及一個圓，判斷圓跟線段的最短距離
- 給你一個三角形（其中一頂點為圓心）和圓形，求出兩者的交集面積
- 兩圓交點
- 圓跟多邊形交集面積
- 最小包覆圓
- 半平面交
- Bentley–Ottmann Algorithm
- Voronoi Diagram
- Delaunay Triangulation
