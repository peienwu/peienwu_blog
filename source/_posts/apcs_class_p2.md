---
title: APCS CLASS PART 2
date: 2021-7-14
tags: 
    - APCS
    - 競賽筆記
categories: APCS檢定
mathjax: true
---

## G. [基礎資料結構 I ] 堆疊、佇列

### a119. 括號問題

```cpp=
#include <bits/stdc++.h>
using namespace std;
stack<int> stk;

int main(){
    string s;
    getline(cin,s);
    int len = s.size(),ans = 0,f=1;
    for(int i=0;i<len;i++){
        if(s[i]=='(')stk.push(1);
        else if(s[i]==')'){
            if(!stk.empty()){
                stk.pop();
                ans++;
            }
            else f = 0;
        }
    }
    if(!stk.empty())f= 0;
    
    if(f)cout<<ans<<endl;
    else cout<<0<<endl;
}

```

### a120. 中置式轉後置式

```cpp=
#include <bits/stdc++.h>
using namespace std;

int main(){
    map<char,int> mp{{'+',1},{'-',1},{'*',2},{'/',2},{'(',0}};
    string s;cin>>s;
    stack<char> st;
    int len = s.size();
    
    for(int i=0;i<len;i++){
        if(s[i]=='(')st.push('(');
        else if(s[i]==')'){
            while(!st.empty()&&st.top()!='('){
                cout<<st.top();
                st.pop();
            }
            st.pop();
        }
        else if(s[i]=='+'||s[i]=='-'||s[i]=='*'||s[i]=='/'){
            while(!st.empty() && mp[st.top()]>=mp[s[i]]){
                cout<<st.top();
                st.pop();
            }
            st.push(s[i]);
        }
        else cout<<s[i];
    }
    while(!st.empty()){
        cout<<st.top();
        st.pop();
    }
    cout<<endl;
}

```

### a121. 電腦算術運算

```cpp=
#include <bits/stdc++.h>
using namespace std;

int main(){
    int n;cin>>n;
    string s;
    while(n--){
        cin>>s;
        stack<int> stk;
        bool legal = 1;
        
        for(int i=0;i<s.size();i++){
            if(s[i]==')'||s[i]==','||s[i]=='(');
            else if(isdigit(s[i])){
                int sum = s[i]-'0';
                while(isdigit(s[++i])){
                    sum=sum*10+(s[i]-'0');
                }
                stk.push(sum);
                i--;
            }
            else if(s[i]=='+'||s[i]=='-'||s[i]=='*'||s[i]=='/'){
                if(s[i+1]!=')'){
                    legal = 0;
                    break;
                }
                int a = stk.top();
                stk.pop();
                int b = stk.top();
                stk.pop();
                if(s[i]=='+')stk.push(a+b);
                else if(s[i]=='-')stk.push(b-a);
                else if(s[i]=='*')stk.push(a*b);
                else if(s[i]=='/'&& a==0){
                    legal = 0;
                    break;
                }
                else if(s[i]=='/')stk.push(b/a);
            }
            else{
                legal = 0;
                break;
            }
        }
        if(legal==0)cout<<-1<<endl;
        else if(stk.size()==1)cout<<stk.top()<<endl;
        else cout<<-1<<endl;
    }
}

```

### a151. 後序式求值

```cpp=
#include <bits/stdc++.h>
using namespace std;

int main(){
    string s;
    while(cin>>s){
        stack<int> stk;
        for(int i=0;i<s.size();i++){
            if(isdigit(s[i]))stk.push(s[i]-'0');
            else{
                int a = stk.top();
                stk.pop();
                int b = stk.top();
                stk.pop();
                if(s[i]=='+')stk.push(a+b);
                else if(s[i]=='-')stk.push(b-a);
                else if(s[i]=='*')stk.push(a*b);
                else if(s[i]=='/')stk.push(b/a);
                else if(s[i]=='%')stk.push(b%a);
            }
        }
        cout<<stk.top()<<endl;
    }
}

```

### a163. 印表機佇列

```cpp=
#include <bits/stdc++.h>
using namespace std;

bool Can(int now_priority,int *p){
    for(int i=now_priority+1;i<=9;i++){
        if(p[i]>=1)return 0;
    }
    return 1;
}

void solve(){
    int n,m,pri[105];cin>>n>>m;
    queue<pair<bool,int>> que;
    memset(pri,0,sizeof(pri));
    for(int i=0;i<n;i++){
        int p;cin>>p;
        if(i==m)que.push({1,p});
        else que.push({0,p});
        pri[p]++;
    }
    
    int ans = 0;
    while(!que.empty()){
        auto cur = que.front();
        que.pop();
        if(Can(cur.second,pri)){
            //可以印的話
            ans++;
            pri[cur.second]--;
            if(cur.first==1)break;
        }
        else que.push(cur);
    }
    cout<<ans<<endl;
}

int main(){
    int t;cin>>t;
    while(t--){
        solve();
    }
}

```

### a164. 團體佇列

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;

int main(){
    ios;
    int t,c=0;
    while(cin>>t && t){
        c++;
        map<int,int> mp;//[成員、團體編號]
        for(int i=0;i<t;i++){
            int n;cin>>n;
            while(n--){
                int x;cin>>x;
                mp[x] = i;
            }
        }
        queue<int> Q,que[1005];//團體順序、團體內順序
        cout<<"Scenario #"<<c<<endl;
        string temp;
        
        while(cin>>temp){
            if(temp[0]=='E'){
                int x;cin>>x;
                if(que[mp[x]].empty())Q.push(mp[x]);
                que[mp[x]].push(x);
            }
            else if(temp[0]=='D'){
                int team = Q.front();
                cout<<que[team].front()<<endl;
                que[team].pop();
                if(que[team].empty())Q.pop();
            }
            else if(temp[0]=='S'){
                cout<<endl;
                break;
            }
        }
    }
}

```

## H. [基礎資料結構 I ] 樹狀結構

### a076. 二元搜尋樹高度

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n,m,tree[3000];
bool used[3000];

int main(){
    ios;
    string s;
    memset(used,0,sizeof(used));
    memset(tree,0,sizeof(tree));
    
    getline(cin,s);
    stringstream ss(s);
    int ans = 0,temp;
    while(ss>>temp){
        int cur = 1,height = 1;
        while(used[cur]){
            if(temp<tree[cur])cur = cur*2;
            else cur = cur*2+1;
            height++;
        }
        tree[cur] = temp;
        used[cur] = 1;
        ans = max(ans,height);
    }
    cout<<ans<<endl;
}

```

### a077. Dropping Balls

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n,m,tree[3000];

int drop(int id){
    if(id>=(1<<(n-1)))return id;
    
    tree[id]=!tree[id];
    if(!tree[id])return drop(2*id+1);
    else return drop(2*id);
}


int main(){
    ios;
    int t;cin>>t;
    while(t--){
        int ans = 0;cin>>n>>m;
        memset(tree,0,sizeof(tree));
        for(int i=0;i<m;i++){
            ans = drop(1);
        }
        cout<<ans<<endl;
    }
}

```

### a122. 血緣關係

```cpp=
#include <bits/stdc++.h>
#define N 100005
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n,start,dis[N];
bool visit[N];
vector<int>edge[N];

int dfs(int id,int cur_dis){  //尋找點id的最遠點距離
    if(visit[id])return 0;
    visit[id] = 1;
    int len = edge[id].size(),max_len=0;
    for(int i=0;i<len;i++){
        int next = edge[id][i];
        if(visit[next])continue;
        dis[next] = cur_dis+1;
        int temp = dfs(next,cur_dis+1);
        max_len = max(max_len,temp);
    }
    return max_len+1;
}

int main(){
    ios;
    cin>>n;
    for(int i=0;i<n-1;i++){
        int a,b;cin>>a>>b;
        edge[a].push_back(b);
        edge[b].push_back(a);
    }
    start = 0;
    memset(visit,0,sizeof(visit));
    int distance = dfs(2,0);distance--;
    for(int i=0;i<n;i++){
        if(dis[i]==distance){
            start = i;
            break;
        }
    }
    memset(visit,0,sizeof(visit));
    cout<<dfs(start,0)-1<<endl;
}

```

### a123. 樹狀圖分析

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define lld long long
#define N 100005
using namespace std;
int n,father[N],h[N];

vector<int>edge[N];

void dfs(int cur){
    int maxn = 0;
    for(auto next:edge[cur]){
        dfs(next);
        maxn = max(maxn,h[next]+1);
    }
    h[cur] = maxn;
}

signed main(){
    ios;
    memset(h,0,sizeof(h));
    memset(father,0,sizeof(father));
    
    cin>>n;
    for(int i=1;i<=n;i++){
        int k;cin>>k;
        for(int j=0;j<k;j++){
            int temp;cin>>temp;
            edge[i].push_back(temp);
            father[temp] = i;
        }
    }
    int root = 1;
    while(father[root]!=0)root++;
    
    dfs(root);
    
    lld ans = 0;
    for(int i=1;i<=n;i++)ans += h[i];
    
    cout<<root<<endl<<ans<<endl;
}


//O(N)的作法
#include <bits/stdc++.h>
#define lld long long
using namespace std;
lld n,m;

int main(){
    int t;scanf("%d",&t);
    while(t--){
        int n,m,ans = 1;scanf("%d %d",&n,&m);
        m--;    //從0開始
        for(int i=1;i<n;i++){         //共n-1步要走
            if(m&1)ans = ans*2+1;     //往右邊走
            else ans = ans*2;
            m = m>>1;                 //由低bit到高bit決定每一步要怎麼走
        }
        printf("%lld\n",ans);
    }
}

```

## I. [基礎資料結構 I ] 圖形結構

### a051. 城市旅遊

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define lld long long
#define N 1000
using namespace std;
int n,m;
bool visit[N];
vector<int>edge[N];

void dfs(int cur){
    visit[cur] = 1;
    for(auto i:edge[cur]){
        if(visit[i])continue;
        dfs(i);
    }
}

void solve(){
    memset(visit,0,sizeof(visit));
    for(int i=0;i<=n+100;i++)edge[i].clear();
    
    for(int i=0;i<m;i++){
        int x,y;cin>>x>>y;
        edge[x].push_back(y);
    }
    int from,to;cin>>from>>to;
    dfs(from);
    if(visit[to])cout<<"Yes"<<endl;
    else cout<<"No"<<endl;
}

signed main(){
    while(cin>>n>>m){
        solve();
    }
}

```

### a102. 油田

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define lld long long
#define N 105
using namespace std;
int n,m,ans = 0;

int dx[8] = {-1,-1,-1,0,1,1,1,0};
int dy[8] = {1,0,-1,-1,-1,0,1,1};

bool visit[N][N],maze[N][N];

void dfs(int x,int y){
    visit[x][y] = 1;
    for(int i=0;i<8;i++){
        int nx = x+dx[i],ny = y+dy[i];
        if(nx<1||nx>n||ny<1||ny>m)continue;
        if(visit[nx][ny])continue;
        if(maze[nx][ny]==1)dfs(nx,ny);
    }
}

void solve(){
    memset(visit,0,sizeof(visit));
    memset(maze,0,sizeof(maze));
    ans = 0;
    
    for(int i=1;i<=n;i++){
        for(int j=1;j<=m;j++){
            char temp;cin>>temp;
            if(temp=='@')maze[i][j] = 1;
            else maze[i][j] = 0;
        }
    }
    for(int i=1;i<=n;i++){
        for(int j=1;j<=m;j++){
            if(maze[i][j]==1 && visit[i][j]==0){
                dfs(i,j);
                ans+=1;
            }
        }
    }
    cout<<ans<<endl;
}

signed main(){
    while(cin>>n>>m && n&& m){
        solve();
    }
}

```

### a103. 小群體

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define lld long long
#define N 50005
using namespace std;

signed main(){
    int n,arr[N];cin>>n;
    for(int i=0;i<n;i++){
        cin>>arr[i];
    }
    bool used[N];
    int ans = 0;
    memset(used,0,sizeof(used));
    
    for(int i=0;i<n;i++){
        if(used[i])continue;
        int cur = arr[i];
        used[cur] = 1;
        while(cur!=i){
            cur = arr[cur];
            used[cur] = 1;
        }
        ans++;
    }
    cout<<ans<<endl;
}

```

### a124. 二分圖

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
#define lld long long
#define N 100005
using namespace std;
int n,m,ans = 0,cnt[2];
vector<int> edge[N];

int color[N];

bool dfs(int cur,int c){
    color[cur] = c;
    cnt[c]+=1;
    bool flag = 1;
    for(auto i:edge[cur]){
        if(color[i]==color[cur])return 0;
        if(color[i]==-1)flag = (flag && dfs(i,!c));
    }
    return flag;
}

void solve(){
    memset(color,0,sizeof(color));
    
    for(int i=0;i<n;i++)color[i] = -1;
    
    for(int i=0;i<m;i++){
        int a,b;cin>>a>>b;a--;b--;
        edge[a].push_back(b);
        edge[b].push_back(a);
    }
    for(int i=0;i<n;i++){
        if(color[i]==-1){
            cnt[0] = 0;cnt[1] = 0;
            if(!dfs(i,0)){
                cout<<0<<endl;
                return;
            }
            ans += min(cnt[0],cnt[1]);
            //有可能是非連通圖，用+=
        }
    }
    cout<<ans<<endl;
}

signed main(){
    cin>>n>>m;
    solve();
}

```

## J. [基礎演算法 I ] 複雜度分析、排序

### a127. 連號或不連號

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld  long double
#define N 24
#define FOR(i,n) for(int i=0;i<n;i++)
using namespace std;
int n;

int main(){
    while(cin>>n){
        set<int>s;
        for(int i=0;i<n;i++){
            int x;cin>>x;
            s.insert(x);
        }
        int maxn = *(--s.end());
        int minn = *s.begin();
        if(s.size()==maxn-minn+1)cout<<minn<<" "<<maxn<<" yes"<<endl;
        else cout<<minn<<" "<<maxn<<" no"<<endl;
    }
}

```

### a128. Agar.io

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld  long double
#define N 10005
#define FOR(i,n) for(int i=0;i<n;i++)
#define pii pair<int,int>
using namespace std;
int n,m,full[N];

set<int> s[N];

int main(){
    cin>>n>>m;
    fill(full,full+n+1,10);
    for(int i=1;i<=n;i++)s[i].insert(i);
    
    while(m--){
        int a,b;cin>>a>>b;
        if(full[b]>full[a])swap(a,b);
        full[a] += full[b];
        full[b] = 0;
        for(auto p:s[b])s[a].insert(p);
        s[b].clear();
    }
    int ans = max_element(full,full+n+1)-full;
    cout<<ans<<endl;
    for(auto p:s[ans])cout<<p<<" ";
    cout<<endl;
}

```

### a129. 飛天桑妮

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld  long double
#define N 10005
#define FOR(i,n) for(int i=0;i<n;i++)
#define pii pair<int,int>
using namespace std;
vector<pii> vec;

bool cmp(pii a,pii b){
    if(a.first==b.first)return a.second>b.second;
    return a.first<b.first;
}

int main(){
    int n,x,y,h;cin>>n;
    for(int i=0;i<n;i++){
        cin>>x>>y>>h;
        vec.push_back({x*x+y*y,h});
    }
    sort(vec.begin(),vec.end(),cmp);
    int ans  = 0,maxh = 0;
    for(int i=0;i<n;i++){
        maxh = max(maxh,vec[i].second);
        ans = max(ans,maxh-vec[i].second);
    }//維護最高的樹
    cout<<ans<<endl;
}

```

### a148. 字元頻率

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld  long double
#define N 24
#define FOR(i,n) for(int i=0;i<n;i++)
#define pii pair<int,int>
using namespace std;

bool cmp(pii a,pii b){
    if(a.second==b.second)return a.first>b.first;
    else return a.second< b.second;
}

int main(){
    string s;
    while(getline(cin,s)){
        map<int,int> mp;    //asci、出現次數
        for(auto temp:s)mp[temp]+=1;
        vector<pii>vec;
        for(auto p:mp){
            vec.push_back(p);
        }
        sort(vec.begin(),vec.end(),cmp);
        for(auto i:vec){
            cout<<i.first<<" "<<i.second<<endl;
        }
        cout<<endl;
    }
}

```

## K. [基礎演算法 I ] 排序與搜尋

### a130. 人員調動

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld  long double
#define N 1005
#define FOR(i,n) for(int i=0;i<n;i++)
#define pii pair<int,int>
using namespace std;

void solve(){
    int wait[N][N];memset(wait,0,sizeof(wait));
    
    int m,ans = 0;cin>>m;
    for(int i=0;i<m;i++){
        int a,b;cin>>a>>b;
        if(wait[b][a]>0){
            ans++;wait[b][a]--;
        }
        else wait[a][b]++;
    }
    cout<<ans<<endl;
}

int main(){
    int t;cin>>t;
    while(t--){
        solve();
    }
}

```

### a131. 大黑馬

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld  long double
#define N 1005
#define FOR(i,n) for(int i=0;i<n;i++)
#define pii pair<int,int>
using namespace std;
int k,n;
int normal[N][N],lower[N][N],upper[N][N];

bool win(int a,int b,int is_normal){
    for(int i=0;i<k+1;i++){
        if(is_normal==0){
            if(normal[a][i]<=normal[b][k+i])return 0;
        }
        else{
            if(upper[a][i]<=lower[b][k+i])return 0;
        }
    }
    return 1;
}

int main(){
    cin>>k>>n;
    for(int i=0;i<n;i++){
        int s;cin>>s;
        for(int j=0;j<2*k+1;j++)cin>>normal[s][j];
        for(int j=0;j<2*k+1;j++)cin>>upper[s][j];
        for(int j=0;j<2*k+1;j++)cin>>lower[s][j];
    }
    for(int p=0;p<=1;p++){
        for(int i=n;i>0;i--){    //從種子序較小的開始
            bool f = 1;
            for(int j=1;j<=n;j++){
                if(!win(i,j,p)){
                    f = 0;
                    break;
                }
            }
            if(f){
                cout<<i<<" ";
                break;
            }
        }
    }
    cout<<endl;
}

```

### a132. 主機排程

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld  long double
#define N 10005
#define FOR(i,n) for(int i=0;i<n;i++)
#define pii pair<int,int>
#define max_ele(a) max_element(a,a+dh)
using namespace std;
int n,dh;

void solve(){
    int s;cin>>s;
    int arr[N]={0},brr[N]={0},crr[N]={0};
    while(s--){
        int a,b,c,d,e,f;cin>>a>>b>>c>>d>>e>>f;
        int start = (a-1)*24+b;
        for(int j=0;j<c;j++){
            arr[(start+j)%dh]+=d;
            brr[(start+j)%dh]+=e;
            crr[(start+j)%dh]+=f;
        }
    }
    cout<<*max_ele(arr)<<" "<<*max_ele(brr)<<" "<<*max_ele(crr)<<endl;
    cin>>s;
}

int main(){
    cin>>n>>dh;
    dh = dh*24;
    while(n--){
        solve();
    }
}

```

### a152. 二分搜尋

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define ld  long double
#define N 1005
#define FOR(i,n) for(int i=0;i<n;i++)
#define pii pair<int,int>
using namespace std;

int main(){
    int n,arr[N];cin>>n;
    for(int i=0;i<n;i++)cin>>arr[i];
    int t;cin>>t;
    
    int pos = 0,ans = 0,find = 0,l = 0,r = n-1;    //[l,r]
    
    while(l<=r){
        int mid = (l+r)/2;
        ans++;
        if(arr[mid]==t){
            pos = mid;find = 1;break;
        }
        else if(arr[mid]>t)r = mid-1;
        else l = mid+1;
    }
    if(find)cout<<pos<<" "<<ans<<endl;
    else cout<<"not found "<<ans<<endl;
}

```

### a153. 二分法求解

```cpp=
#include <bits/stdc++.h>
int main(){
  std::cout<<std::fixed<<std::setprecision(6)<<log(2)<<std::endl;
}

```

## L. [基礎演算法 I ] 窮舉法

### a088. 最大乘積

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define N 20
#define FOR(i,n) for(int i=0;i<n;i++)
#define pii pair<int,int>
#define pid pair<int,double>
#define pdi pair<double,int>
using namespace std;
int n,m;

void solve(){
    int c = 0;
    while(cin>>n){
        c++;
        int dp[N][N];memset(dp,0,sizeof(dp));
        int ans = 0;
        FOR(i,n){
            int temp;cin>>temp;
            dp[i][i] = temp;
            ans = max(ans,temp);
        }
        for(int i=0;i<n;i++){
            for(int j=i+1;j<n;j++){
                dp[i][j] = dp[i][j-1]*dp[j][j];
                ans = max(dp[i][j],ans);
            }
        }
        cout<<"Case #"<<c<<": The maximum product is "<<max(ans,(ll)0)<<"."<<endl;
    }
}

signed main(){
    Orz;
    int t = 1;
    while(t--){
        solve();
    }
}

```

### a133. 採蘑菇攻略問題

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define N 51
#define FOR(i,n) for(int i=0;i<n;i++)
#define pii pair<int,int>
#define pid pair<int,double>
#define pdi pair<double,int>
using namespace std;
int n,m;

void solve(){
    cin>>n;
    int ans = 0,dp[N][N];
    memset(dp,0,sizeof(dp));
    
    FOR(i,n){
        int temp;cin>>temp;
        dp[i][i] = temp;
        ans = max(ans,temp);
    }
    for(int i=0;i<n;i++){
        for(int j=i+1;j<n;j++){
            dp[i][j] = dp[i][j-1]+dp[j][j];
            ans = max(ans,dp[i][j]);
        }
    }
    cout<<ans<<endl;
}

signed main(){
    Orz;
    int t = 1;
    while(t--){
        solve();
    }
}

```

### a134. 回文日期問題

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define N 51
#define FOR(i,n) for(int i=0;i<n;i++)
#define pii pair<int,int>
#define pid pair<int,double>
#define pdi pair<double,int>
using namespace std;
int n,m;

vector<int>ans;

bool leap(int temp){
    return (((temp%4)==0 &&(temp%100)!=0)||(temp%400)==0);
}

void check(string s){
    string temp = s;
    reverse(temp.begin(),temp.end());
    if(temp==s)ans.push_back(stoi(s));
}

void solve(){
    cin>>n;
    ans.clear();
    int month[13] = {0,31,28,31,30,31,30,31,31,30,31,30,31};
    month[2]+=((leap(n))?1:0);
    for(int i=1;i<=12;i++){
        for(int j=1;j<=month[i];j++){
            check(to_string(n)+to_string(i)+to_string(j));
            if(i<10)check(to_string(n)+"0"+to_string(i)+to_string(j));
            if(j<10)check(to_string(n)+to_string(i)+"0"+to_string(j));
            if(i<10 && j<10)check(to_string(n)+"0"+to_string(i)+"0"+to_string(j));
        }
    }
    sort(ans.begin(),ans.end());
    cout<<ans.size();
    for(auto i:ans)cout<<" "<<i;
    cout<<endl;
}

signed main(){
    Orz;
    int t;cin>>t;
    while(t--){
        solve();
    }
}

```

### a135. 巧克力擺盒

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define N 51
#define FOR(i,n) for(int i=0;i<n;i++)
#define pii pair<int,int>
#define pid pair<int,double>
#define pdi pair<double,int>
using namespace std;
int n,m,k;
int r[9],box[9];;

bool cmp(vector<int> r){
    for(int i=0;i<9;i+=3){
        if(r.size()==3){
            if((r[0]&box[i])&&(r[1]&box[i+1])&&(r[2]&box[i+2])){
                return true;
            }
            //對於這一行的指定是有滿足的，回傳正確（下一個要求繼續再呼叫一次）
        }
        else if(((r[0]&box[i])&&(r[1]&box[i+1]))||((r[0]&box[i+1])&&(r[1]&box[i+2]))){
            return true;
        }
    }
    return false;
}

vector<int> rule;
vector<vector<int>> rule_list;

void solve(){
    for(int i=0;i<9;i++){
        int temp=1;temp = temp<<i;
        r[i] = temp;
        box[i] = temp;
    }
    map<string,int> mp = {
        {"PS",r[0]},
        {"PC",r[1]},
        {"PT",r[2]},
        {"P?",r[0]|r[1]|r[2]},
        {"BS",r[3]},
        {"BC",r[4]},
        {"BT",r[5]},
        {"B?",r[3]|r[4]|r[5]},
        {"YS",r[6]},
        {"YC",r[7]},
        {"YT",r[8]},
        {"Y?",r[6]|r[7]|r[8]},
        {"?S",r[0]|r[3]|r[6]},
        {"?C",r[1]|r[4]|r[7]},
        {"?T",r[2]|r[5]|r[8]},
        {"??",511},
    };
    cin>>n;
    while(n--){
        cin>>k;
        rule.resize(k);
        for(int i=0;i<k;i++){
            char temp[3];cin>>temp[0]>>temp[1];
            rule[i] = (mp[temp]);
        }
        rule_list.push_back(rule);
    }
    int ans = 0;
    do{
        ans += all_of(rule_list.begin(),rule_list.end(),cmp);
        
    }while(next_permutation(box,box+9));
    
    cout<<ans<<endl;
}

signed main(){
    Orz;
    int t=1;
    while(t--){
        solve();
    }
}

```

### a154. 除法

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define N 51
#define FOR(i,n) for(int i=0;i<n;i++)
#define pii pair<int,int>
#define pid pair<int,double>
#define pdi pair<double,int>
using namespace std;
int n,m;
bool used[10],f;

bool check(int t){
    bool cur_used[10]={0};
    if(t<10000&&used[0]==1)return 0;
    else if(t<10000)cur_used[0] = 1;
    
    while(t>0){
        int d = t%10;
        if(cur_used[d])return 0;
        else if(used[d])return 0;
        cur_used[d] = 1;
        t /= 10;
    }
    return 1;
}

void dfs(int first,int id){
    if(id>=5){
        if(first%n!=0)return;
        int second = first/n;
        if(second<1234)return;
        if(check(second)){
            f = 1;
            cout<<first<<" / ";
            if(second<10000)cout<<0;
            cout<<second<<" = "<<n<<endl;
            return;
        }
    }
    for(int i=0;i<10;i++){
        if(used[i])continue;
        used[i] = 1;
        dfs(first*10+i,id+1);
        used[i] = 0;
    }
}

void solve(){
    while(cin>>n){
        if(n==0)return;
        memset(used,0,sizeof(used));
        f=0;
        dfs(0,0);
        if(f==0)cout<<"There are no solutions for "<<n<<"."<<endl;
        cout<<endl;
    }
}

signed main(){
    Orz;
    int t = 1;
    while(t--){
        solve();
    }
}

```
