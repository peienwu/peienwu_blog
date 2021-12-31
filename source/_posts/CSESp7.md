---
title: CSES:Geometry
date: 2021-9-30
tags: 
    - CSES
categories:
    - CSES
mathjax: true
---

## Geometry

### Point Location Test

[題目連結](https://cses.fi/problemset/task/2189/)
<!--more-->
```cpp=
#include <bits/stdc++.h>
#define int long long
#define double long double
#define pii pair<int, int>
#define N 10000
#define x first
#define y second
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;

struct pt{
    int x,y;
    bool operator == (pt b){
        if(x == b.x && y == b.y)return true;
        return false;
    }
    pt operator - (pt b){return {x - b.x , y - b.y};}
    pt operator + (pt b){return {x + b.x , y + b.y};}
    int operator ^ (pt b){return (x * b.y - y * b.x);}
    int operator * (pt b){return (x * b.x + y * b.y);}
};

signed main(){
    IOS;
    int t;cin>>t;
    while(t--){
        int x1,y1,x2,y2,x3,y3;
        cin>>x1>>y1>>x2>>y2>>x3>>y3;
        pt l = {x2 - x1,y2 - y1}, o = {x3 - x1,y3 - y1};
        
        if((l ^ o) == 0)cout<<"TOUCH"<<"\n";
        else if((l ^ o) > 0)cout<<"LEFT"<<"\n";
        else cout<<"RIGHT"<<"\n";
    }
}
```

### Line Segment Intersection

[題目連結](https://cses.fi/problemset/task/2190/)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define double long double
#define pii pair<int, int>
#define N 10000
#define x first
#define y second
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;

struct pt{
    int x,y;
    bool operator == (pt b){
        if(x == b.x && y == b.y)return true;
        return false;
    }
    pt operator - (pt b){return {x - b.x , y - b.y};}
    pt operator + (pt b){return {x + b.x , y + b.y};}
    int operator ^ (pt b){return (x * b.y - y * b.x);}
    int operator * (pt b){return (x * b.x + y * b.y);}
};

bool onseg(pt a,pt b,pt o){
    int cross = (a - o) ^ (b - o);
    int dot = (a - o) * (b - o);
    return cross == 0 && dot <= 0;
    //dot == 0 when o is a or b
}

int dir(pt a,pt b,pt o){
    int cross = (a - o) ^ (b - o);
    if(cross == 0)return 0;
    else if(cross > 0)return 1;
    else return -1;
}

bool inter(pt a,pt b,pt c,pt d){
    if(onseg(a,b,c) || onseg(a,b,d))return true;
    if(onseg(c,d,a) || onseg(c,d,b))return true;
    if((dir(a,b,c) * dir(a,b,d)) == -1
       && (dir(c,d,a) * dir(c,d,b)) == -1)
        return true;
    return false;
}

signed main(){
    IOS;
    int t;cin>>t;
    while(t--){
        pt pot[4];
        for(int i=0;i<4;i++)cin>>pot[i].x>>pot[i].y;
        if(inter(pot[0],pot[1],pot[2],pot[3]))cout<<"YES"<<"\n";
        else cout<<"NO"<<"\n";
    }
}
```


### Polygon Area

[題目連結](https://cses.fi/problemset/task/2191/)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define double long double
#define pii pair<int, int>
#define N 1005
#define x first
#define y second
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n;

struct pt{
    int x,y;
    bool operator == (pt b){
        if(x == b.x && y == b.y)return true;
        return false;
    }
    pt operator - (pt b){return {x - b.x , y - b.y};}
    pt operator + (pt b){return {x + b.x , y + b.y};}
    int operator ^ (pt b){return (x * b.y - y * b.x);}
    int operator * (pt b){return (x * b.x + y * b.y);}
}pot[N];

int solve(){
    int ans = 0;
    for(int i=0;i<n;i++)ans += (pot[i] ^ pot[i+1]);
    return ans;
}

signed main(){
    IOS;
    cin>>n;
    for(int i=0;i<n;i++)cin>>pot[i].x>>pot[i].y;
    pot[n] = pot[0];
    cout<<abs(solve())<<"\n";
}
```


### Point in Polygon

[題目連結](https://cses.fi/problemset/task/2192/)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define double long double
#define pii pair<int, int>
#define N 1005
#define INF 1000000005
#define x first
#define y second
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n,m;

struct point{
    int x,y;
    bool operator == (point b){
        if(x == b.x && y == b.y)return true;
        return false;
    }
    point operator - (point b){return {x - b.x , y - b.y};}
    point operator + (point b){return {x + b.x , y + b.y};}
    int operator ^ (point b){return (x * b.y - y * b.x);}
    int operator * (point b){return (x * b.x + y * b.y);}
}pt[N];

bool onseg(point a,point b,point o){
    int cross = (a - o) ^ (b - o);
    int dot = (a - o) * (b - o);
    return (cross == 0) && (dot <= 0);
}

int dir(point a,point b,point o){
    int cross = (a - o) ^ (b - o);
    if(cross == 0)return 0;
    else if(cross > 0)return 1;
    else return -1;
}

bool inter(point a,point b,point c,point d){
    if(onseg(a,b,c) || onseg(a,b,d))return true;
    if(onseg(c,d,a) || onseg(c,d,b))return true;
    if(dir(a,b,c) * dir(a,b,d) < 0 && dir(c,d,a) * dir(c,d,b) < 0)
        return true;
    return false;
}

int solve(point cur){
    int sum = 0;
    for(int i=0;i<n;i++){
        if(onseg(pt[i],pt[i+1],cur) == 1)return -1;
        if(inter(pt[i],pt[i+1],cur,point{INF,cur.y}))sum++;
        point temp = pt[i].y > pt[i+1].y ? pt[i] : pt[i+1];
        if(temp.y == cur.y && temp.x > cur.x)sum--;
    }
    return sum;
}

signed main(){
    IOS;
    cin>>n>>m;
    for(int i=0;i<n;i++)cin>>pt[i].x>>pt[i].y;
    pt[n] = pt[0];
    for(int i=0;i<m;i++){
        point temp;cin>>temp.x>>temp.y;
        int ans = solve(temp);
        if(ans == -1)cout<<"BOUNDARY"<<"\n";
        else if(ans & 1)cout<<"INSIDE"<<"\n";
        else cout<<"OUTSIDE"<<"\n";
    }
}
```


### Polygon Lattice Points

[題目連結](https://cses.fi/problemset/task/2193/)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define double long double
#define pii pair<int, int>
#define N 100005
#define INF 1000005
#define x first
#define y second
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n,m;

struct point{
    int x,y;
    bool operator == (point b){
        if(x == b.x && y == b.y)return true;
        return false;
    }
    point operator - (point b){return {x - b.x , y - b.y};}
    point operator + (point b){return {x + b.x , y + b.y};}
    int operator ^ (point b){return (x * b.y - y * b.x);}
    int operator * (point b){return (x * b.x + y * b.y);}
}pt[N];

int solve(){
    int sum = 0;
    for(int i=0;i<n;i++)sum += pt[i] ^ pt[i+1];
    return abs(sum);
}

int gcd(int a,int b){
    if(b == 0)return a;
    return gcd(b,a%b);
}

signed main(){
    IOS;
    cin>>n;
    for(int i=0;i<n;i++)cin>>pt[i].x>>pt[i].y;
    pt[n] = pt[0];
    int cnt = 0,ans = 0;
    for(int i=0;i<n;i++)
        cnt += gcd(abs(pt[i].x-pt[i+1].x),abs(pt[i].y-pt[i+1].y));
    ans = (solve() + 2 - cnt)/2;
    cout<<ans<<" "<<cnt<<"\n";
}
```


### Minimum Euclidean Distance

[題目連結](https://cses.fi/problemset/task/2194/)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define double long double
#define pii pair<int, int>
#define N 200005
#define INF LONG_LONG_MAX
#define x first
#define y second
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n;
vector <pii> pt;
 
int dis(pii a,pii b){
    int x = a.x - b.x,y = a.y - b.y;
    return x * x + y * y;
}
 
bool cmp(pii a,pii b){
    return a.y < b.y;
}
 
vector <pii> temp;
//區間[l,r]
int solve(int l,int r){
    if(l == r)return INF;
    int mid = (l + r) / 2,mid_x = pt[mid].x;
    int ans = min(solve(l,mid),solve(mid+1,r));
    
    temp.assign(r - l + 1,{0 , 0});
    merge(
          pt.begin() + l,pt.begin() + mid + 1,
          pt.begin() + mid + 1,pt.begin() + r + 1,
          temp.begin(), cmp
    );
    for(int i=l;i<=r;i++)pt[i] = temp[i-l];
    temp.clear();
    for(int i=l;i<=r;i++){
        if(abs(pt[i].x - mid_x)*abs(pt[i].x - mid_x) <= ans)temp.push_back(pt[i]);
    }
    int len = temp.size();
    for(int i = 0;i < len;i++){
        for(int j = i+1;j < len;j++){
            ans = min(ans,dis(temp[i],temp[j]));
            if(abs(temp[i].y - temp[j].y)*abs(temp[i].y - temp[j].y) > ans)break;
        }
    }
    return ans;
}
 
signed main(){
    IOS;
    cin>>n;
    pt.assign(n,{0,0});
    for(int i=0;i<n;i++)cin>>pt[i].x>>pt[i].y;
    sort(pt.begin(),pt.end());
    cout<<solve(0,n-1)<<"\n";
}
```


### Convex Hull

[題目連結](https://cses.fi/problemset/task/2195/)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define double long double
#define pii pair<int, int>
#define N 200005
#define INF LONG_LONG_MAX
#define x first
#define y second
#define all(a) a.begin(),a.end()
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n,m;

struct point{
    int x,y;
    bool operator == (point b){
        if(x == b.x && y == b.y)return true;
        return false;
    }
    point operator - (point b){return {x - b.x , y - b.y};}
    point operator + (point b){return {x + b.x , y + b.y};}
    int operator ^ (point b){return (x * b.y - y * b.x);}
    int operator * (point b){return (x * b.x + y * b.y);}
};
vector<point> pt;

bool cmp(point a,point b){
    if(a.x == b.x)return a.y < b.y;
    return a.x < b.x;
}

bool dir(point a,point b,point c){
    point A = a - c,B = b - c;
    return (A ^ B) >= 0;
}

vector<point> convex(){
    vector<point> h;
    sort(all(pt),cmp);
    for(auto i : pt){
        while(h.size() > 1 && !dir(h[h.size()-1],i,h[h.size()-2]))
            h.pop_back();
        h.push_back(i);
    }
    int down = h.size();
    h.pop_back();
    reverse(all(pt));
    for(auto i : pt){
        while(h.size() > down && !dir(h[h.size()-1],i,h[h.size()-2]))
            h.pop_back();
        h.push_back(i);
    }
    h.pop_back();
    return h;
}

signed main(){
    IOS;
    cin>>n;
    pt.assign(n,{0,0});
    for(int i=0;i<n;i++)cin>>pt[i].x>>pt[i].y;
    vector<point> ans = convex();
    cout<<ans.size()<<"\n";
    for(auto i : ans)cout<<i.x<<" "<<i.y<<"\n";
}
```
