---
title: 資芽第十三週：根號算法
date: 2021-6-5
tags: 
    - 資芽筆記
    - 根號算法
categories: 資訊之芽筆記
mathjax: true
---

## 上課內容

好噁心喔，複雜度竟然帶根號！？ 根號算法有很多例題，最常聽到（或遇到？）的就是**分塊**了！把序列分成$\sqrt{K}$ 塊，在做RMQ（區間加值、區間求和等）的其中一種方式

1. RMQ區間極值詢問（可以用分塊做、線段樹、BIT、稀疏表sparse table）
2. Counting Triangle
3. 分塊（值域分塊、塊狀鍊表等）
4. **操作分塊**

其實根號算法的重點其實在於複雜度分析（講師花很多時間講不同作法的複雜度分析），怎麼樣分析讓複雜度跑出根號

<!--more-->

## 上機作業

### Counting Triangles

[題目連結](https://neoj.sprout.tw/problem/252/)
我個人認為做法超級精妙的！有很多種作法，挑一個比較好實作的：
{% note success %}
假設可以 $O(1)$ 回答 (x, y) 是否為圖中的一個邊。對於每條邊 (u, v) ，你花$O(min(d_u, d_v))$去算出包含這條邊的三角形個數
總複雜度：$O(M\sqrt{M})$
{% endnote %}

![](https://i.imgur.com/SazG9dP.png)

其中對於均攤查詢一條邊有沒有在圖中，總邊數$O(M)$ $\div$ 詢問每一條邊$O(M)$ = 均攤$O(1)$的複雜度，實作起來非常簡單

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define N 100001
#define M 100001
using namespace std;

int main(){
    ios;
    vector<int> G[N],query[N];  //G是存圖、query是存詢問
    int n,m,x[M],y[M];  //x,y存邊用
    bool visit[N];
    
    cin>>n>>m;
    for(int i=0;i<m;i++){
        cin>>x[i]>>y[i];
        G[x[i]].push_back(y[i]);
        G[y[i]].push_back(x[i]);
    }
    int sml = 0,big = 0;
    for(int i=0;i<m;i++){    //保證複雜度為根號的關鍵
        if(G[x[i]].size()<=G[y[i]].size())sml = x[i];
        else sml = y[i];    //尋找哪一個度數比較小
        big = x[i]+y[i]-sml;
        for(int j : G[sml]){   //訪問跟sml有相鄰的所有邊，看有沒有(j,big)
            query[j].push_back(big);
        }
    }
    int ans = 0;
    for(int i=0;i<n;i++){
        //均攤O(1)?
        for(int j:G[i]){
            visit[j] = true;
        }
        for(int j:query[i]){
            ans+=visit[j];
        }
        for(int j:G[i]){
            visit[j] = false;
        }
    }
    cout<<ans/3<<endl;
}
```

### 中國人插隊問題

[題目連結](https://neoj.sprout.tw/problem/213/)
如果用一個vector 存整個序列，query $O(1)$，但新增跟刪除都是$O(N)$
因此，如果改用分塊的方法，用deque下去砸，每K個分成一塊，對於插入與刪除都是$O(K+\frac{N}{K})$，詢問是$O(1)$
根據算幾不等式，我們可以取$K = \sqrt{N}$ 時，複雜度可以達到$O(\sqrt{N})$ ，很不錯吧！

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;

signed main(){
    ios;
    int n,m,K;
    cin>>n>>m;
    K = sqrt(n);
    deque<int> deq[n/K+(m/K)+10];
    for(int i=0;i<n;i++){
        int temp;cin>>temp;
        deq[i/K].push_back(temp);
    }
    for(int i=0;i<m;i++){
        string a;cin>>a;
        if(a[0]=='A'){
            int x,y;cin>>y>>x;y--;
            deq[y/K].insert(deq[y/K].begin()+(y%K), x);
            int len = deq[y/K].size(),cur = y/K;
            while(len>K){
                int temp = deq[cur].back();
                deq[cur].pop_back();
                cur++;
                deq[cur].push_front(temp);
                len = deq[cur].size();
            }
        }
        else if(a[0]=='L'){
            int x;cin>>x;x--;
            deq[x/K].erase(deq[x/K].begin()+(x%K));
            int len = deq[x/K].size(),cur = x/K;
            while(len<K && deq[cur+1].size()!=0){
                int temp = deq[cur+1].front();
                deq[cur+1].pop_front();
                deq[cur].push_back(temp);
                cur++;
                len = deq[cur].size();
            }
        }
        else if(a[0]=='Q'){
            int x;cin>>x;x--;
            int len = x%K;
            auto it = deq[x/K].begin();
            cout<<*(it+len)<<endl;
        }
    }
}
```

### 第 Z 小

[題目連結](https://neoj.sprout.tw/problem/742/)
超討厭，一直RE，結果是卡在沒有開long long，哭啊
這一題是值域分塊，一樣是根據值域每K個分成一塊，去維護每一大塊的數字數量總和，這樣在查詢(query)的時候花 $O(C/K)$ 找到相應大塊(C為值域)，再花 $O(K)$ 的時間掃過小塊，而加值減值都是$O(1)$可以處理
複雜度：$O(K+\frac{C}{K})$ 取 K=$\sqrt{C}$ 有最小值：$O(Q\sqrt{C})$

```cpp=
#include <bits/stdc++.h>
#define int long long
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define N 100005
using namespace std;    //值域分塊
const int K = 100;

signed main(){
    ios;
    int q,arr[N],mn[N/K+10];cin>>q;
    memset(arr, 0, sizeof(arr));
    while(q--){
        int temp;cin>>temp;
        if(temp==1){
            int x,y;cin>>x>>y;
            arr[x]+=y;
            mn[x/K]+=y;
        }
        else if(temp==2){
            int x,y;cin>>x>>y;
            arr[x]-=y;
            mn[x/K]-=y;
        }
        else if(temp==3){
            int z,sum = 0,ind=0;cin>>z; //ind查詢位於第幾塊
            while(sum+mn[ind]<z)sum+=mn[ind++];
            int cur_ind = ind*K;
            for(int i=0;i<=K;i++){
                if(arr[cur_ind+i]){
                    sum+=arr[cur_ind+i];
                    if(sum>=z){
                        cout<<ind*K+i<<endl;
                        break;
                    }
                }
            }
        }
    }
}
```

### 小咲的玩具

[題目連結](https://neoj.sprout.tw/problem/722/)
這一題的常數超緊，因此在嘗試使用*hash*時用*unordredmap*被卡*TLE*，在很電的人提示後，要使用黑魔法cc_hash_table才可能會過關

```cpp=
#include <ext/pb_ds/assoc_container.hpp>
using namespace __gnu_pbds;
cc_hash_table<int,int>cc;
```

他的用法其實跟*unordered_map*幾乎一樣吧（至少基本的insert跟find的語法都一樣），不過他可以做到比*unordered_map*更好的效率。不過我用[最近點對](https://neoj.sprout.tw/problem/795/)這一題測試效率卻發現黑魔法會tle，可能是在clear這一個步驟的效率並不是很好吧！

總之，就是對於每一筆詢問(x,y)，不失一般性假設size_y > size_x，則對於y排序並預處理前綴和，同時枚舉x中的所有點並二分搜他在y的位置
最重要的，是對每一筆詢問都儲存起來，這樣複雜度會瞬間少一個N
**補複雜度證明**

```cpp=
#include <bits/stdc++.h>
using namespace std;
#include <ext/pb_ds/assoc_container.hpp>
using namespace __gnu_pbds;
#define int long long
#define N 150006

int n,k,q;
vector<int> vec[N],pre[N];
bool visit[N];
cc_hash_table<int,int>cc;
 
int HASH(int x,int y){
    return x*1000000007+y;
}
signed main(){
    scanf("%lld %lld %lld",&n,&k,&q);
    memset(visit, 0, sizeof(visit));
    while(k--){
        int c,p;scanf("%lld %lld",&c,&p);
        vec[p].push_back(c);
    }
    while(q--){
        int x,y;scanf("%lld %lld",&x,&y);
        if(vec[x].size()>vec[y].size())swap(x, y);
        
        if(cc.find(HASH(x,y))!=cc.end()){
            printf("%lld\n",cc.find(HASH(x,y))->second);
            continue;
        }
        else if(cc.find(HASH(y,x))!=cc.end()){
            printf("%lld\n",cc.find(HASH(y,x))->second);
            continue;
        }
        
        int len = vec[y].size();
        if(!visit[y]){
            sort(vec[y].begin(),vec[y].end());
            pre[y].resize(len+6);
            for(int i=0;i<=len;i++)pre[y][i+1] = pre[y][i]+vec[y][i];
            visit[y] = 1;
        }
        
        int ans = 0;
        for(int j:vec[x]){
            int pos = lower_bound(vec[y].begin(), vec[y].end(),j)-vec[y].begin();
            pos--;
            if(pos<0)ans+=(len*j);
            else ans += (pre[y][pos+1]+j*(len-pos-1));
        }
        printf("%lld\n",ans);
        cc.insert(make_pair(HASH(x,y),ans));
        cc.insert(make_pair(HASH(y,x),ans));
    }
}
```

## 手寫作業

這已經是最後的一堂課了qq，13週的課程就這樣結束了！
![](https://i.imgur.com/o8xCu1i.gif)
![](https://i.imgur.com/tevltPd.png)
手寫作業排名12，上機不知道，但二階段因為剛好二段的關係，有一個禮拜（dp3）的課的上機作業全部放掉（我在幹嘛qq）那真是慘啊！
