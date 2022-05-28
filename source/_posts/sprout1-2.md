---
title: 資芽1-4週：基礎資結、枚舉
date: 2021-3-13
tags: 
    - 資芽筆記
    - 樹
    - 資結
categories: 資訊之芽筆記
mathjax: true
---

# 資芽第一週：基礎資結、複雜度、樹

第一次上課，原本想說會輕鬆的度過一個禮拜
沒想到直接用單調隊列當作開場，讓第一個星期圍繞著stack 與queue的資結世界中
但也很慶幸的，在第一週終於學會用很節簡的程式碼寫單調隊列
第二週的內容則是**複雜度分析**
P 與NP 問題真讓人一頭是大，雖然在競程中對於較複雜的複雜度問題很少派上用場
不過還是蠻有趣的，只要證明P=NP就可以很多錢了！

<!--more-->

## 上課內容

第一週主題：基礎資料結構（queue, stack, 單調隊列）
第二週主題：複雜度分析、樹狀結構

## 上機作業

挑幾題比較有趣的

### 大善人老闆救濟東南亞兒童

[題目連結](https://neoj.sprout.tw/problem/19/)
![](https://i.imgur.com/1MHCZeb.png)
這一題很熟悉，在去年9月剛開學的資讀學長有講過，結果那時候根本聽不懂QQ
但現在來看，這一題就是模擬stack ，很基礎的題目
可以用queue (也可以不用)配上stack，分別模擬Station 跟B站 的情況

```cpp=
#include <bits/stdc++.h>
using namespace std;
int t;

signed main(){
    cin>>t;
    while(t--){
        int n,arr[100005];cin>>n;
        stack<int>s;
        queue<int>q;
        for(int i=0;i<n;i++){
            cin>>arr[i];
            q.push(arr[i]);
        }
        
        for(int i=0;i<n;i++){
            s.push(i+1);//push 進station
            while(!s.empty()&&!q.empty()&&s.top()==q.front()){
                s.pop();q.pop();
            }
        }
        if(s.empty()&&q.empty())cout<<"Yes"<<endl;
        else cout<<"No"<<endl;
    }
}
```

### 中國人排隊問題

[題目連結](https://neoj.sprout.tw/problem/20/)
這一題有一個條件有點難維護，就是如果同一團體的人要排隊
而隊中已經有同一團人在裡面時，他就要排在團裡面最後一個位置
因此要用iterator 陣列指向每個團隊的末端之類的
好好維護**細節**可以減少debug 的時間！

```cpp=
#include <bits/stdc++.h>
using namespace std;
int n,cnt = 1;

int main(){
    while(cin>>n){
        cout<<"Line #"<<cnt<<endl;
        int arr[1000005],gt[1005];//紀錄在哪一個團體當中
        memset(arr, 0, sizeof(arr));
        memset(gt, 0, sizeof(gt));
        for(int i=1;i<=n;i++){
            int k;cin>>k;
            for(int j=0;j<k;j++){
                int temp;cin>>temp;
                arr[temp] = i;
            }
        }
        list<int> ls;
        list<int>::iterator it[1005];//指向第i個團體的尾端
        while(true){
            string s;int cur;
            cin>>s;
            if(s[0]=='E'){
                cin>>cur;
                int i = arr[cur];
                if(i>0){
                    if(gt[i]==0){
                        ls.push_back(cur);
                        it[i] = --ls.end();
                    }
                    else{
                        it[i] = ls.insert(++it[i],cur);
                    }
                    gt[i]++;
                }
                else ls.push_back(cur);
            }
            else if(s[0]=='D'){
                int i = ls.front();
                if(arr[i]>0)gt[arr[i]]--;
                cout<<ls.front()<<endl;
                ls.pop_front();
            }
            else break;
        }
        cnt++;
    }
}
```

### 陸行鳥大賽車

[題目連結](https://neoj.sprout.tw/problem/21/)
這一題我嘗試了兩個方法
第一個方法：用vector 紀錄名次，但就是會TLE ，原因：需要O(N)找到當前的車車在第幾名，如果遭受攻擊，也要用O(N)來erase vector的元素，結果長這樣：
![](https://i.imgur.com/FNJVrB8.png)
講師說：不要用vector 的erase ，因為他會耗費O(N)的時間

第二個方法：用一個struct 的陣列，裡面包了每一台車的資訊包含他的名次，這樣就可以用O(1)更改每一台車的名次，算是第一個算法的優化

```cpp=
#include <bits/stdc++.h>
using namespace std;
int N,M;
struct node{
    int player_id;
    node *next,*prev;
    node(){
        next = NULL;
        prev = NULL;
    }
};
node *player[100005];

signed main(){
    ios
    cin>>N>>M;
    for(int i=0;i<=N+2;i++){
        player[i] = new node();
        player[i]->player_id = i;
    }
    for(int i=1;i<=N+2;i++){
        player[i]->prev = player[i-1];
        player[i]->next = player[i+1];
    }//初始化設定
    for(int i=0;i<M;i++){
        int a,b;cin>>a>>b;
        if(a==0){
            player[b]->prev->next = player[b]->next;
            player[b]->next->prev = player[b]->prev;
        }//編號b的車車被消滅
        else{
            if(!player[b]->prev->prev)continue;
            int pp,p,n;
            pp = player[b]->prev->prev->player_id;
            p = player[b]->prev->player_id;
            n = player[b]->next->player_id;
            
            player[pp]->next = player[b];
            player[b]->prev = player[pp];
            player[b]->next = player[p];
            player[p]->next = player[n];
            player[n]->prev = player[p];
            player[p]->prev = player[b];
        }//交換名次的部分，不過應該有方法可以更簡單
    }
    stack<int> s;
    int ind = N+1;
    while(player[ind]->prev!=NULL){
        if(ind!=N+1){
            s.push(player[ind]->player_id);
        }
        ind = player[ind]->prev->player_id;
    }
    while(!s.empty()&&s.size()!=1){
        cout<<s.top()<<" ";
        s.pop();
    }
    cout<<s.top()<<endl;
}
```

### 超大螢幕設置

[題目連結](https://neoj.sprout.tw/problem/513/)
講師在課堂上有講解這個題目的算法，就是對每個「大樓」（剛剛看才知道）分別往左往右看（單調隊列），看到第一個小於自己就停止
單調隊列問了講師之後，參照他的寫法，用vector 儲存「位置的index」而不要用pair ，明顯增加了程式碼的易讀性！

**這是用pair 版本**：

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0);cin.tie(0);
#define int long long
using namespace std;
int n;

signed main(){
    ios
    cin>>n;
    vector<pair<int,int>> vec;
    for(int i=0;i<n;i++){
        int temp;cin>>temp;
        vec.push_back(make_pair(temp, i+1));
    }
    int ans[n+5][2];
    stack<pair<int, int>> p;
    p.push(vec[0]);
    
    for(int i=1;i<n;i++){
        if(vec[i].first >= p.top().first){
            p.push(vec[i]);
        }
        else{
            while(!p.empty() && p.top().first > vec[i].first){
                ans[p.top().second-1][0] = i-p.top().second+1;
                p.pop();
            }
            p.push(vec[i]);
        }
    }
    while(!p.empty()){
        ans[p.top().second-1][0] = n-p.top().second+1;
        p.pop();
    }
    p.push(vec[n-1]);
    
    for(int i=n-2;i>=0;i--){
        if(vec[i].first >= p.top().first){
            p.push(vec[i]);
        }
        else{
            while(!p.empty() && p.top().first > vec[i].first){
                ans[p.top().second-1][1] = p.top().second-i-1;
                p.pop();
            }
            p.push(vec[i]);
        }
    }
    while(!p.empty()){
        ans[p.top().second-1][1] = p.top().second;
        p.pop();
    }
    
    int total = 0;
    for(int i=0;i<n;i++){
        int sum =0;
        sum = vec[i].first*(ans[i][0]+ans[i][1]-1);
        total = max(total,sum);
    }
    cout<<total<<endl;
}
```

而這個是用講師習慣的作法，明顯短了很多：

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0);cin.tie(0);
#define int long long
using namespace std;
int n;

signed main(){
    ios
    cin>>n;
    int arr[n+5],lft[n+5],rht[n+5];
    memset(arr,0,sizeof(arr));
    
    for(int i=1;i<=n;i++)cin>>arr[i];
    vector<int> stk(1,0);
    
    for(int i=1;i<=n;i++){
        while(arr[i]<=arr[stk.back()])stk.pop_back();
        lft[i] = i-stk.back();
        stk.push_back(i);
    }
    while(!stk.empty())stk.pop_back();
    
    stk.push_back(n+1);
    
    for(int i=n;i>=1;i--){
        while(arr[i]<=arr[stk.back()])stk.pop_back();
        rht[i] = stk.back()-i-1;
        stk.push_back(i);
    }
    int ans[n+5];
    for(int i=1;i<=n;i++){
        ans[i-1] = arr[i]*(lft[i]+rht[i]);
    }
    cout<<*max_element(ans,ans+n)<<endl;
}
```

### 檸檬汽水傳說（NPSC）

[題目連結](https://neoj.sprout.tw/problem/22/)
這題我覺得很難！
一開始想說維護一個遞增的單調隊列，沒想到一直吃WA
好吧，上網找答案
結果是發現，題目很機車的需要處理相同元素的情況
因為一排一樣的數字可以橫跨很多個數字，形成數對
好不容易處理完之後上傳code 又吃WA
debug 了好久，才發現是因為第一個連續出現的數字好好處理
因為第一個數字就算跟後面連續的數字相同也不能再跟前面構成任何數對！
總算，經過好久好久，終於才AC ，真是得來不易！

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define int long long
using namespace std;
int n,t;

signed main(){
    ios;
    cin>>t;
    while(t--){
        cin>>n;
        vector<int> s;
        
        int ans = 0,cnt[1000005];
        memset(cnt, 0, sizeof(cnt));//紀錄s當下該數出現次數
        while(n--){
            int k,top = s.size();cin>>k;
            
            while(!s.empty() && s.back()<k){
                cnt[top--] = 0;
                s.pop_back();
                ans++;
            }
            if(s.empty())s.push_back(k);
            else{
                if(s.back()==k && top>1){
                    cnt[top+1]+=cnt[top]+1;
                    ans+=cnt[top+1];
                }
                s.push_back(k);ans++;
            }
        }
        cout<<ans<<endl;
    }
}
```

### 二元搜尋樹

[題目連結](https://neoj.sprout.tw/problem/48/)
很特別的一題，原來BST 用中序遍歷就是排序好的數列！
有了前序跟中序之後，就可以透過遞迴來還原整棵二元樹了！

```cpp=
#include <bits/stdc++.h>
using namespace std;
int n;

struct Node{
    Node *left;
    Node *right;
    int val;
};

Node* BinaryTree(int* preorder, int* inorder, int length){
    if(length==0)return NULL;
    Node *node = new Node;
    node->val = *preorder;
    
    int root_index = 0;
    for(;root_index<length;root_index++){
        if(inorder[root_index]==*preorder)break;
    }
    node->left = BinaryTree(preorder+1, inorder,root_index);
    node->right = BinaryTree(preorder+root_index+1, inorder+root_index+1,length-root_index-1);
    cout<<node->val<<endl;
    return node;
}

signed main(){
    int arr[2005],sorted[2005];
    while(cin>>arr[n]){
        sorted[n] = arr[n];
        n++;
    }
    sort(sorted, sorted+n);
    BinaryTree(arr, sorted, n);
}
```

### 樹重心

[題目連結](https://neoj.sprout.tw/problem/293/)
超愛這一題，竟然可以用DFS 跑一次O(N)把所有的點的資訊全部求出來
透過簡單的運算把看起來很複雜的題目很優美的解出來！

```cpp=
#include <bits/stdc++.h>
using namespace std;
int t;

vector<int> Edge[100005];
bool visit[100005];

struct Node{
    int sum;//子節點總數（扣掉算父節點）
    int maxn;//子數最大的那一個
}node[100005];

int dfs(int id){
    visit[id] = true;
    int len = Edge[id].size();
    for(int i=0;i<len;i++){
        int temp = Edge[id][i];
        if(visit[temp]==1)continue;
        int t = dfs(temp);
        node[id].sum += t;
        node[id].maxn = max(node[id].maxn,t);
    }
    return node[id].sum+1;
}

signed main(){
    cin>>t;
    while(t--){
        int n;cin>>n;
        memset(visit, 0, sizeof(visit));
        
        for(int i=0;i<n;i++){//initialize
            Edge[i].clear();
            node[i].maxn = node[i].sum = 0;
        }
        for(int i=0;i<n-1;i++){
            int a,b;cin>>a>>b;
            Edge[a].push_back(b);
            Edge[b].push_back(a);
        }
        dfs(0);
        int ans = n,index = 0;
        for(int i=0;i<n;i++){
            int temp = max(node[i].maxn,n-node[i].sum-1);//子與父求慘度
            if(temp<ans){
                index = i;
                ans = temp;
            }
        }
        cout<<index<<endl;
    }
}
```

## 手寫作業

這兩週手寫都是數學證明！
有夠悲慘，因為題目很多都不會啊，還要去問老師，結果老師也沒有給出一個好的解方FF
換個角度想，就算脫離數學證明，作業一樣不會簡單到哪裡去的！

# 資芽第三週：基礎圖論、淹水問題

前兩次上課還沒來得及補齊，先來紀錄一下第三週的內容

## 上課內容

這一週的主題是基礎圖論、淹水問題(BFS)還有Heap!把這幾個單元排在一起真的是負擔很重QQ
<!--more-->
* 基礎圖論之前就有聽過，所以在寫題目感覺還好，不會很吃力
* Heap則是我第一次接觸到的資料結構，當周上課前把影片（介紹Binary Heap）之後，上課就直接講合併Heap、黑魔法Heap、左偏樹等等把我電爛的東西
* 淹水問題，上課沒有特別介紹，因為講師說他不喜歡*Flood Fill* 演算法所以只講了可以用 *dx* 和 *dy* 表示上下左右的方位，其他的都沒講，只能自己看影片了呀～

<font color="#f00">這一週是段考週呀</font> 我得花一些時間讀段考，不然段考要爆炸了...</font>

## 上機作業

### Heap 練習

[題目連結](https://neoj.sprout.tw/problem/59/)
刻一個heap嘛，不想刻就call stl( *std::priorityqueue* 就不放上來了)
自己刻一個binary heap ，沒有想象中的簡單，有些細節會不小心漏掉

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define int long long
using namespace std;
int t,top=1,heap[1000005];//complete binary tree

void push(int n){
    heap[top] = n;
    int father_pos = top/2,pos = top;
    while(father_pos>0 && heap[father_pos]>heap[pos]){
        swap(heap[father_pos], heap[pos]);
        pos = father_pos;
        father_pos/=2;
    }
    top = top+1;
}

int pop(){
    int ans = heap[1];
    heap[1] = heap[--top];
    heap[top] = 0;
    int now = 1,left = now*2, right = now*2+1;
    
    int temp = min(heap[right],heap[left]);
    while(heap[left]!=0 && heap[right]!=0 && heap[now]>temp){
        if(heap[now]>heap[right] && heap[left]>heap[right]){
            swap(heap[right], heap[now]);
            now = right;
        }
        else if(heap[now]>heap[left]){
            swap(heap[left], heap[now]);
            now = left;
        }
        right = now*2+1;
        left = now*2;
        temp = min(heap[right],heap[left]);
    }
    if(heap[right]!=0 && heap[now]>heap[right])swap(heap[right], heap[now]);
    else if(heap[left]!=0 && heap[now]>heap[left])swap(heap[left], heap[now]);
    return ans;
}
bool empty(){
    if(heap[1]==0)return 1;
    else return 0;
}
signed main(){
    ios;
    cin>>t;
    memset(heap, 0, sizeof(heap));
    while(t--){
        int a;cin>>a;
        if(a==1){
            int b;cin>>b;
            push(b);
        }
        else if(a==2){
            if(empty())cout<<"empty!"<<endl;
            else cout<<pop()<<endl;
        }
    }
}
```

### 哪裡有卦，哪裡就有源(TOJ 1101)

[題目連結](https://neoj.sprout.tw/problem/1101/)
明顯的二分圖水題喔～
用 dfs 把經過的點標出黑白，過程中如果遇到問題就表示非二分圖了

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define int long long
using namespace std;
int n;

bool visit[100005],color[100005];
vector<int> edge[100005];

bool dfs(int id, bool col){
    visit[id] = 1;
    color[id] = col;
    int len = edge[id].size();
    for(int i=0;i<len;i++){
        int temp = edge[id][i];
        if(visit[temp]){
            if(color[temp]==col)return 0;
            else continue;
        }
        if(!dfs(temp, !col))return 0;
    }
    return 1;
}
signed main(){
    ios;
    while(cin>>n && n){
        int m;cin>>m;
        for(int i=0;i<n;i++)edge[i].clear();
        memset(visit, 0, sizeof(visit));
        memset(color, 0, sizeof(color));
        while(m--){
            int a,b;cin>>a>>b;
            edge[a].push_back(b);
            edge[b].push_back(a);
        }
        bool f = 1;
        for(int i=0;i<n;i++){
            if(!visit[i]){
                if(!dfs(i, 0)){
                    f = 0;
                    break;
                }
            }
        }
        if(f)cout<<"NORMAL."<<endl;
        else cout<<"RAINBOW."<<endl;
    }
}
```

### 喵喵抓老鼠

[題目連結](https://neoj.sprout.tw/problem/44/)
這一題是用BFS找最短路徑，因為沒有權重，所以可以直接用BFS看多久會最先走到老鼠
x y 座標原本用2個queue 分別存x跟y ，沒想到MLE了，後來改用struct，還是MLE!!
不過在27行的地方有一個Bug，害我找了好久...去問了<font color="#f00">電神</font>，才發現 visit 應該要在push 的時候就紀錄了，不要等到pop的時候，不然很多重複的會被push 進去（簡單來說，一個位置只能被push一次）

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define MAX 102
using namespace std;
int n,sx,sy;
int dx[4]={-1,0,1,0}, dy[4]={0,-1,0,1};//左下右上
char maze[MAX][MAX];
bool visit[MAX][MAX];

struct node{
    int x,y,path;
};
queue<node> qq;
int bfs(){
    int now_x = sx, now_y = sy;
    qq.push(node{sx,sy,0});
    while(!qq.empty()){
        now_x = qq.front().x;
        now_y = qq.front().y;
        if(maze[now_x][now_y]=='@')return qq.front().path;
        int front = qq.front().path+1;
        for(int i=0;i<4;i++){
            int nx = now_x+dx[i],ny = now_y+dy[i];
            if(!visit[nx][ny] && maze[nx][ny]!='#'){
                qq.push(node{nx,ny,front});
                visit[nx][ny] = 1;
            }
        }
        qq.pop();
    }
    return -1;
}
signed main(){
    ios;
    while(cin>>n && n!=0){
        memset(visit, 0, sizeof(visit));
        while(!qq.empty())qq.pop();
        for(int i=0;i<n;i++)cin>>maze[i];
        
        for(int i=0;i<n;i++)
            for(int j=0;j<strlen(maze[i]);j++)
                if(maze[i][j]=='K'){
                    sx = i;sy = j;
                    break;
                }
        int ans = bfs();
        if(ans>=0)cout<<ans<<"\n";
        else cout<<"= =\""<<"\n";
    }
    return 0;
}
```

### 庭院裡的水池

[題目連結](https://neoj.sprout.tw/problem/42/)
看圖形有幾個連通圖的判斷的水題，可以用dfs 或bfs 做，小心index不要戳到負的

**BFS作法**

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int t;
bool maze[1005][1005],visit[1005][1005];
int dx[4]={-1,0,1,0}, dy[4]={0,1,0,-1};
queue<int> qx,qy;

void bfs(int x,int y){
    int now_x=x,now_y=y;
    qx.push(x);
    qy.push(y);
    while(!qx.empty()){
        now_x = qx.front();now_y = qy.front();
        visit[now_x][now_y]=1;
        for(int i=0;i<4;i++){
            int nx = now_x+dx[i], ny = now_y+dy[i];
            if(!visit[nx][ny] && maze[nx][ny]==1){
                qx.push(nx);
                qy.push(ny);
                visit[nx][ny] = 1;
            }
        }
        qx.pop();
        qy.pop();
    }
}
int main(){
    ios;
    cin>>t;
    while(t--){
        int a,b;cin>>a>>b;
        memset(maze,0,sizeof(maze));
        memset(visit,0,sizeof(visit));
        for(int i=1;i<=a;i++){
            for(int j=1;j<=b;j++){
                char temp;cin>>temp;
                if(temp=='#')maze[i][j]=0;
                else maze[i][j]=1;
            }
        }
        int ans = 0;
        for(int i=1;i<=a;i++){
            for(int j=1;j<=b;j++){
                if(maze[i][j]==1 && visit[i][j]==0){
                    bfs(i,j);
                    ans++;
                }
            }
        }
        cout<<ans<<endl;
    }
}
```

**DFS作法**

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int t;
bool maze[1005][1005],visit[1005][1005];
int dx[4]={-1,0,1,0}, dy[4]={0,1,0,-1};
queue<int> qx,qy;

void dfs(int x, int y){
    visit[x][y] = 1;
    for(int i=0;i<4;i++){
        int nx = x+dx[i], ny = y+dy[i];
        if(!visit[nx][ny] && maze[nx][ny]==1){
            dfs(nx, ny);
        }
    }
}
int main(){
    ios;
    cin>>t;
    while(t--){
        int a,b;cin>>a>>b;
        memset(maze,0,sizeof(maze));
        memset(visit,0,sizeof(visit));
        for(int i=1;i<=a;i++){
            for(int j=1;j<=b;j++){
                char temp;cin>>temp;
                if(temp=='#')maze[i][j]=0;
                else maze[i][j]=1;
            }
        }
        int ans = 0;
        for(int i=1;i<=a;i++){
            for(int j=1;j<=b;j++){
                if(maze[i][j]==1 && visit[i][j]==0){
                    dfs(i, j);
                    ans++;
                }
            }
        }
        cout<<ans<<endl;
    }
}
```

比較上面兩種方法的複雜度
![](https://i.imgur.com/WfcJ5I8.png)
BFS明顯優於DFS 啊！（遞迴本身來說就很耗記憶體?!應該是這題特殊的原因，不然應該用DFS 較優）

### 染色遊戲—台北縣98資訊學科能力競賽

[題目連結1](https://neoj.sprout.tw/problem/46/)[題目連結2](https://neoj.sprout.tw/problem/46/)
> 一題好題勝過百題水題

現在終於體會到這一句話的精髓！這是一題難題呀（neoj的測資很緊）
首先先看**顏色儲存的方式**
如果我想要讓混色時一個運算進行，那要如何以數字形式儲存顏色就顯德相當重要
可以發現，用加法並不是個粉好的方法，因為顏色的數字有可能超過7
因此可以用取OR的方式來完成：

>YELLOW : 001
BLUE : 010
GREEN : 011
RED : 100
ORANGE : 101
PURPLE : 110
DARK(BLACK) : 111

如果以這樣的編碼方式，剛剛好可以以OR運算來就可以模擬染色（這應該只能用觀察出來吧）
有了顏色的編碼方式之後，接下來就是處理**點擴散**的問題
由 $n<=1000$ 可知，如果要用2個for迴圈走訪整個畫布是不可行的
因為這樣就已經$10^6$ 多幾個常數就TLE了。
不能走訪每一個點，就只能好好的把要看的點push進queue裡面
每一次針對要找的節點，就能節省不必要的運算

最後，要怎麼維護**點**呢？
用一個*struct* 儲存，除了座標之外，就是出現的時間還有顏色
{% note success %}
**我遇到TLE 或 WA的BUG：**

1. 用 $n^2$ 掃描整個畫布—TLE
2. push進一個struct時要在名字部分**括號起來**：

```cpp=
queue<node> q;
q.push((node){1,2,3,4});
```

3. 沒有加上遇到減少就break的條件，所以每一次都必須要整個畫布都變成黑色才會輸出—TLE
4. 沒有紀錄一個點有沒有被三原色走過，一個點可能被走過很多次—TLE
5. 邊界要好好維護，一個較方便的作法，讓index都從1開始，這樣相加相減變成-1 時才不會RE
6. 時間優化：明顯知道答案（如問D的情況），就直接輸出$n^2$就好
{% endnote %}

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0);
using namespace std;
int n,col[150],dx[8] = {-1,0,1,1,1,0,-1,-1},
    dy[8] = {-1,-1,-1,0,1,1,1,0},board[1005][1005];
bool visit[1005][1005][5];//三原色每一個點只能被走一次

int encode(char a){
    col['Y'] = 1;col['B'] = 2;col['G'] = 3;col['R'] = 4;
    col['O'] = 5;col['P'] = 6;col['D'] = 7;
    return col[a];
}
struct node{
    int x,y,time,color;
};

int main(){
    ios;
    int t;cin>>t;
    while(t--){
        cin>>n;
        queue<node> qq;
        memset(board, 0, sizeof(board));
        memset(visit, 0, sizeof(visit));
        char a;
        for(int i=0;i<3;i++){
            int b,c;cin>>a>>b>>c;b++;c++;
            qq.push((node){b,c,1,encode(a)});
            board[b][c] = encode(a);
        }
        cin>>a;
        int target = encode(a),cur_sum = 0,ans = 0,time = 1;
        if(a=='B'||a=='Y'||a=='R')cur_sum++;//當要求的是三原色，初始的數量可以加一
        
        if(a=='D'){
            cout<<n*n<<endl;
            continue;
        }//如果是D的話，只需要輸出n^2，可以節省不少時間
        while(!qq.empty()){//BFS 開始
            while(!qq.empty() && qq.front().time==time){//開始每一個時刻的模擬
                int nx = qq.front().x,ny = qq.front().y;
                for(int i=0;i<8;i++){
                    int x = nx+dx[i],y = ny+dy[i];
                    if(x<=0 ||x>n||y<=0||y>n||board[x][y]==7||
                       visit[x][y][qq.front().color])continue;//超出邊界或曾拜訪或已塗黑可跳過
                    qq.push((node){x,y,time+1,qq.front().color});//push下一時刻的node
                    int new_color = board[x][y]|qq.front().color;//比較新的顏色與舊的顏色
                    if(new_color!=board[x][y]){//顏色不同時要比較狀況
                        if(new_color==target)cur_sum++;//新顏色是但舊顏色不一樣 則加一
                        else if(board[x][y]==target)cur_sum--;//舊的是新的不是 則減一
                    }
                    board[x][y]|=qq.front().color;//對新方塊上色
                    visit[x][y][qq.front().color] = 1;//此顏色已經拜訪
                }
                qq.pop();//pop from queue
            }
            time++;//一輪模擬結束
            if(ans>cur_sum)break;//因為數量會先非嚴格遞增、後非嚴格遞減
            ans = max(ans, cur_sum);
        }
        cout<<ans<<endl;
    }
}

```

## 手寫作業

這一週的手寫作業是介紹c++ 的記憶體使用方式，什麼變數應該會被存在什麼位置之類的，heap 與stack 還有虛擬記憶體，以及為什麼有時候寫dfs遞迴下去會RE的問題（我是還沒有遇過啦）
![](https://i.imgur.com/mFgJxBF.png)
總之終於沒有數學證明題了！

---
<font color="#f00">今天段考果然炸裂了QQQQQQQ</font>

# 資芽第四週：枚舉算法

## 上課內容

這一週的主題是**枚舉(enumeration)**，內容主要環繞二分搜、三分搜、DFS剪枝這些部分。
這一週的內容算是相對輕鬆，解數獨的部分也是蠻有趣的！
<!--more-->

## 上機作業

### 數獨

[題目連結](https://neoj.sprout.tw/problem/62/)
首先是數獨，很有趣，之前有做過類似的是**八皇后問題**
用到**DFS剪枝**，[筆記在這](https://peienwu.com/2021/03/07/Sudoku/)

```cpp=
#include <bits/stdc++.h>
using namespace std;

class Sudoku{
private:
    int maze[9][9];
    bool flag = false;
public:
    void print(string s);//
    void scan_maze();//
    vector<int> select(int, int);
    int next_empty(int, int);
    void solving();
    void dfs(int row, int col );
    
};
void Sudoku::print(string s){
    cout<<endl<<"====="<<s<<"======"<<endl;
    for (int i=0; i<9;i++){
        cout<<"|";
        for (int j=0;j<9;j++){
            cout<<maze[i][j];
            if(j%3==2)cout<<"|";
        }
        cout<<endl;
        if(i%3==2)cout<<"-------------"<<endl;
    }
}
vector<int> Sudoku::select(int r, int c){
    bool box[9]={0};
    for(int i=0;i<9;i++){
        if(maze[i][c]>0)box[maze[i][c]-1] = 1;
        if(maze[r][i]>0)box[maze[r][i]-1] = 1;
    }
    int row_start = 3*(r/3),col_start = 3*(c/3);
    for(int i=0;i<3;i++){
        for(int j=0;j<3;j++){
            if(maze[row_start+i][col_start+j]>0)
                box[maze[row_start+i][col_start+j]-1] = 1;
        }
    }
    vector<int> ans;
    for(int i=0;i<9;i++)
        if(!box[i])ans.push_back(i+1);
    return ans;
}
int Sudoku::next_empty(int row, int col){
    int ind = col;
    for(int i=row;i<9;i++){
        while(ind<9){
            if(maze[i][ind]==0){
                int pos = i*9+ind;
                return pos;
            }
            ind++;
        }
        ind = 0;
    }
    return -1;
}
void Sudoku::dfs(int row, int col ){
    int pos = next_empty(row, col),nr,nc;
    if(pos == -1){
        flag = true;
        return;
    }
    vector<int> candidate = select(row, col);
    int len = candidate.size();
    
    for(int i=0;i<len;i++){
        maze[row][col] = candidate[i];
        pos = next_empty(row, col);
        nr = pos/9; nc = pos%9;
        dfs(nr, nc);
        if(flag)return;
    }
    maze[row][col] = 0;
}
void Sudoku::scan_maze(){
    for(int i=0;i<9;i++){
        for(int j=0;j<9;j++){
            char temp;cin>>temp;
            if(temp=='.')maze[i][j] = 0;
            else maze[i][j] = temp -'0';
        }
    }
}
void Sudoku::solving(){
    int first_empty = next_empty(0, 0),nr,nc;
    nr = first_empty/9; nc = first_empty%9;
    dfs(nr, nc);
    bool f = false;
    for(int i=0;i<9;i++){
        for(int j=0;j<9;j++){
            vector<int> v = select(i,j);
            if(v.size()>0)f = true;
        }
    }
    if(!flag || f)cout<<"No solution."<<endl;
    else{
        print("solved");
    }
    flag = false;
}
int main(){
    Sudoku sodoku1;
    sodoku1.scan_maze();
    sodoku1.print("begin");
    sodoku1.solving();
}
```

### Lotto(UVA)

[題目連結](https://neoj.sprout.tw/problem/63/)
這一題主要就是建立一個排列樹，把每一種可能的情況用遞迴列出來
![](https://i.imgur.com/cKGOtgP.png)
這一題用DFS明顯比BFS來得好、**空間**省很多（用一下資芽的圖XD)
![](https://i.imgur.com/sOjMiuk.png)

來看程式碼吧！

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n,arr[15];

void permutation(int arr_ind, int cur[],int cur_ind){
    if(arr_ind>n)return;
    if(cur_ind>=6){
        for(int i=0;i<6;i++){
            if(i>0)cout<<" ";
            cout<<cur[i];
        }
        cout<<endl;
        return;
    }
    for(int i = 0; i <= n-(6-cur_ind); i++){
        cur[cur_ind] = arr[arr_ind+i];
        permutation(arr_ind+i+1, cur, cur_ind+1);
    }
}

bool f=0;

int main(){
    ios;
    while(cin>>n){
        if(n==0)break;
    if(f)cout<<endl;
    memset(arr, 0, sizeof(arr));
    for(int i=0;i<n;i++)cin>>arr[i];
    sort(arr, arr+n);
    int cur[10];
    permutation(0, cur, 0);
    f=1;
    }
}
```

{% note warning %}
這一題必須很小心輸入輸出，它們會讓你WA很久呀！

1. 行末不要多餘空白
2. 測資全部印出來後，末端**不要多餘換行**

大概就這兩點
{% endnote %}

### 田忌賽馬(NPSC)

[題目連結](https://neoj.sprout.tw/problem/69/)
之前有寫過這一題，不過現在再寫一次還是WA了很久，對於二分搜的維護（到底是取到0還是1）還要更熟悉
除此之外，我因為邊界問題（$10^8$ 或是$10^8+1$）沒有仔細注意就一直WA

```cpp=
#include <bits/stdc++.h>
#define N 10000 
using namespace std;  
lli n,k,t,M,upper = 100000000,lower = -1;  
lli mine[N],develop[N],enemy[N],curr[N];  

bool win(lli day){  
    int it = 0,sum = 0;  
    for(int i=0;i<n;i++)  
        curr[i] = mine[i]+day*develop[i];  
    sort(curr, curr+n);  
    for(int i=0;i<n;i++){  
        if(curr[i]>enemy[it]){  
            sum++;  
            it++;  
        }  
    }  
    return (sum>=k);  
}  
  
int main(){  
    ios::sync_with_stdio(false);  
    cin.tie(0);  
    cin>>t;  
    while(t--){  
        upper = 100000000;lower = -1;  
        cin>>n>>k;  
        for(int i=0;i<n;i++){  
            cin>>mine[i]>>develop[i];  
        }  
        for(int i=0;i<n;i++){  
            cin>>enemy[i];  
        }  
        sort(enemy, enemy+n);  
        if(!win(upper))cout<<-1<<endl;  
        else{  
            while(upper-lower!=1){  
                M = (upper+lower)/2;  
                if(win(M))upper = M;  
                else lower = M;  
            }  
            cout<<upper<<endl;  
        }  
    }  
}  
```

{% note success %}
這樣維護二分搜也可以，就是保證 r 一定是1（l就不一定）

```cpp=
while(r>l){
    int mid = (l+r)/2, arr[n+5];
    for(int i=0;i<n;i++)arr[i] = mine[i]+mid*rate[i];
        sort(arr, arr+n,greater<int>());
        int f = can_win(arr, enemy);
        if(f)r = mid;
        else l = mid+1;
    }
```

單純二分搜，找01分界點！
{% endnote %}

### Happiness Function(2013 台清交程式設計競賽)

[題目連結](https://neoj.sprout.tw/problem/72/)
很酷的題目，首先必須做一點數學上的分析
{% note info %}
假設題目給定的n個二次函數依序為$A_1(x),A_2(x),...,A_n(x)$
令函數$f(t)=max(A_j(t), 1<=j<=n)$
則$f(t)$為一個U型函數
{% endnote %}
有了這個性質之後就可以利用**三分搜**來找U型函數的最小值!

值得注意的是：

1. *float* 是**單精度**浮點數、*double* 是**雙精度**浮點數，我用 float吃了好幾個WA，結果用double 直接 AC
2. while 迴圈裡面的判斷不要用浮點數相減，會出事（TLE）

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define float a
using namespace std;
int T,n;
double a[12],b[12],c[12];

double func(double t, int i){
    return a[i]*(t-b[i])*(t-b[i])+c[i];
}

int main(){
    ios;
    cout<<fixed<<setprecision(5);
    cin>>T;
    while(T--){
        cin>>n;
        for(int i=0;i<n;i++)cin>>a[i]>>b[i]>>c[i];
        double left = 0.0, right = 300.0;
        double now_l = 0,now_r = 0,nl = 0,nr = 0;
        int cnt = 0;
        while(cnt<=10000){
            nl = (2*left+right)/3;
            nr = (2*right+left)/3;
            now_l = now_r = 0;
            for(int i=0;i<n;i++)now_r = max(now_r, func(nr,i));
            for(int i=0;i<n;i++)now_l = max(now_l, func(nl,i));
            if(now_r > now_l)right = nr;
            else left = nl;
            cnt++;
        }
        cout<<now_l<<endl;
    }
}
```

簡單統整，就是三分搜找最低點！

### 東方古墓古文

[題目連結](https://tioj.ck.tp.edu.tw/problems/73)
這一題主要是二分搜工作量上限X，寫一個函數判斷是否工作量X是否可行
Debug 超久，不知道在幹嘛，結果最後是卡在二分搜的上界範圍不夠大
要開到$10^9$這麼大！
時間複雜度：$N$$log$(總工作量)

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0);
#define int long long
using namespace std;
int t;
int n,m,arr[100005];

bool is_ok(int lim){
    int sum = 0,ans = 1;
    for(int i=0;i<n;i++){
        if(arr[i]>lim)return 0;
        sum += arr[i];
        if(sum+arr[i+1] > lim){
            sum = 0;
            ans++;
        }
    }
    if(ans>m)return 0;
    else return 1;
}

signed main(){
    ios;
    cin>>t;
    while(t--){
        cin>>n>>m;
        memset(arr, 0, sizeof(arr));
        for(int i=0;i<n;i++)cin>>arr[i];
        arr[n] = -INT_MAX;
        int r = 1000000001,l = 0;
        while(r-l > 1){
            int mid = (l+r)/2;
            if(is_ok(mid))r = mid;
            else l = mid;
        }
        if(r == 1000000001)cout<<1000000000<<endl;
        else cout<<r<<endl;
    }
}
```

### 極速馬拉松

[題目連結](https://tioj.ck.tp.edu.tw/problems/60)
這一題難的主要是變數太多
只要理解它到底要幹嘛，就可以發現可以利用$O(N)$枚舉出固定去間內最大的距離
之後再$O(logN)$ 二分搜時間
就可以用$O(logN)$ AC這一題

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0);
#define int long long
using namespace std;
int a,b,c,d,m,s,t;

int func(int time){
    int speed_time = m/c,max_distance = 0,cur_m = m%c;
    int cur_distance = b*speed_time,cur_t = speed_time;

    if(cur_t<time){
        int len = time-cur_t;
        for(int i=0;i<=len;i++){//時限內最多可以跑多遠
            cur_distance = b*speed_time;
            cur_t = speed_time+i;
            cur_m = (m%c)+d*i;

            int step = min(cur_m/c,time-cur_t);

            cur_m = cur_m-step*c;
            cur_t+=step;
            cur_distance += b*step;

            cur_distance+=a*(time-cur_t);
            max_distance = max(max_distance,cur_distance);
        }
    }
    else max_distance = b*time;
    return max_distance;
}

signed main(){
    ios;
    cin>>a>>b>>c>>d>>m>>s>>t;
    int max_distance = func(t);
    if(s>=max_distance)cout<<"No"<<endl<<max_distance<<endl;
    else{
        cout<<"Yes"<<endl;
        int r = t,l = 0;
        while(r-l>1){
            int mid = (r+l)/2;
            if(func(mid)>s)r = mid;
            else l = mid;
        }
        cout<<r<<endl;
    }
}
```

### 人生低潮

[題目連結](https://tioj.ck.tp.edu.tw/problems/1926)

## 手寫作業

手寫作業介紹字串比對、各種排序演算法。這是我算是第一次搞懂快速排序在幹嘛，不然之前都是看兩個指針在指來指去不知道確切的功能是什麼。
除了快排之外，基數排序也是第一次看到，原來排序可以做到**線性時間複雜度！**
大開眼界！（不過只是用於整數排序）
這次手寫覺得有些困難，不過學到了很多東西！（可以來做一個各種排序演算的筆記）
