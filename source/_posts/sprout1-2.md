---
title: 資芽第一、二週：基礎資結、複雜度、樹
date: 2021-3-13
tags: 
    - 資芽筆記
    - 樹
    - 資結
categories: 資訊之芽筆記
mathjax: true
---

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
