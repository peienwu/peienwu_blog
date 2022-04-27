---
title: APCS CLASS PART 3
date: 2021-7-15
tags: 
    - APCS
    - 競賽筆記
categories: APCS檢定
mathjax: true
---
## M. [基礎資料結構 II 及基礎演算法 II ] 貪婪法

### a071. 排隊買飲料

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
    priority_queue<int,vector<int>,greater<>>pq;
    
    cin>>n>>m;
    int ans=0,time=0;
    for(int i=1;i<=n;i++){
        cin>>time;
        if(i>m){
            time+=pq.top();
            pq.pop();
        }
        pq.push(time);
        ans = max(ans,time);
    }
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

### a075. 醜數

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define double long double
#define N 14
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define rrep(i,l,r) for(int i=l;i<r;i++)
#define pii pair<int,int>
using namespace std;
int n;

signed main(){
    Orz;
    cin>>n;
    vector<int> vec;
    vec.push_back(1);
    int id2 = 0,id3 = 0,id5 = 0;
    //用前面的醜數推後面的，不會有其他質樹
    for(int i=1;i<n;i++){
        int ugly = min(vec[id2]*2,min(vec[id3]*3,vec[id5]*5));
        vec.push_back(ugly);
        if(vec[i]==vec[id2]*2)id2+=1;
        if(vec[i]==vec[id3]*3)id3+=1;
        if(vec[i]==vec[id5]*5)id5+=1;
    }
    cout<<vec[n-1]<<endl;
}

```

### a091. Add All

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
    while(cin>>n && n){
        priority_queue<int,vector<int>,greater<int>> pq;
        for(int i=0;i<n;i++){
            int temp;cin>>temp;
            pq.push(temp);
        }
        int ans = 0;
        while(pq.size()!=1){
            int cur = pq.top();
            pq.pop();
            cur += pq.top();
            pq.pop();
            ans+=cur;
            pq.push(cur);
        }
        cout<<ans<<endl;
    }
}

signed main(){
    Orz;
    int t=1;
    while(t--){
        solve();
    }
}

```

### a139. 背包置物問題

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define double long double
#define N 14
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define rrep(i,l,r) for(int i=l;i<r;i++)
#define pii pair<int,int>
using namespace std;
int n,k;
vector<int> vec,p;

signed main(){
    cin>>n;
    vec.assign(1,-1);
    cin.ignore();
    string s;getline(cin,s);
    stringstream ss(s);
    int temp;
    while(ss>>temp)vec.push_back(temp);
    cin>>k;
    
    int ans = 0,len = vec.size();
    for(int i=1;i<=len-1;i++){
        if(find(p.begin(),p.end(),vec[i])!=p.end())continue;
        else if(p.size()<k){
            p.push_back(vec[i]);
            continue;
        }
        int index = 0, dis = 0, max_dis=0;
        
        for(int j=0;j<p.size();j++){
            dis = 0;
            for(int q=i+1;q<=len;q++){
                if(vec[q]==p[j])break;
                dis += 1;
            }
            if(max_dis < dis){
                max_dis = dis;
                index = j;
            }
        }
//        cout<<i<<" "<<index<<endl;
        p[index] = vec[i];
        ans += 1;
    }
    cout<<ans<<endl;
}

```

### a141. 基地台

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define N 50005
using namespace std;
int n,k,arr[N];

//確認直徑可否覆蓋全部（greedy）
bool check(int sum){
    int max_cover = arr[0]+sum;
    int cnt=1;
    for(int i=1;i<n;i++){
        if(max_cover < arr[i]){
            max_cover = arr[i]+sum;
            cnt++;
        }
    }
    return (cnt<=k);
}

void solve(){
    cin>>n>>k;
    for(int i=0;i<n;i++)cin>>arr[i];
    sort(arr,arr+n);
    int l = 1,r = ceil((arr[n-1]-arr[0])/k);
    //區間[l,r]二分搜尋直徑
    while(l<r){
        int mid = (l+r)/2;
        if(check(mid))r = mid;
        else l = mid+1;
    }
    cout<<l<<endl;
}

signed main(){
    Orz;
    int t;t = 1;
    while(t--){
        solve();
    }
}

```

## N. [基礎資料結構 II 及基礎演算法 II ] 分而治之與回溯法

### a089. 蘇丹王位繼承者

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define double long double
#define N 10
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define rrep(i,l,r) for(int i=l;i<r;i++)
#define pii pair<int,int>
using namespace std;
int n,chess[N][N],Q[N],ans=0;

bool check(int row,int col){
    for(int i=1;i<row;i++){
        if(col == Q[i]||abs(row-i)==abs(col-Q[i]))return 0;
    }
    return 1;
}

void Queen(int row){
    if(row>8){
        int sum = 0;
        rep(i,1,8)sum += chess[i][Q[i]];
        ans = max(ans,sum);
        return;
    }
    for(int i=1;i<=8;i++){
        if(check(row,i)){
            Q[row] = i;
            Queen(row+1);
        }
    }
}

void solve(){
    memset(chess,0,sizeof(chess));
    memset(Q,0,sizeof(Q));
    ans = 0;
    for(int i=1;i<=8;i++){
        for(int j=1;j<=8;j++){
            cin>>chess[i][j];
        }
    }
    Queen(1);
    cout<<right<<setw(5)<<ans<<endl;
    
}

signed main(){
    int t;cin>>t;
    while(t--){
        solve();
    }
}

```

### a090. 質數環

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define double long double
#define N 20
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define rrep(i,l,r) for(int i=l;i<r;i++)
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int n,prime[105],ans[N],cnt = 0;
bool visit[N];

void init(){
    rep(i,0,100)prime[i] = 1;
    prime[1] = 0;
    for(int i=2;i<=100;i++){
        if(prime[i]==0)continue;
        for(int j=2*i;j<=100;j+=i){
            prime[j] = 0;
        }
    }
}

void dfs(int id,int val){
    ans[id] = val;
    if(id>=n-1){
        if(!prime[val+1])return;
        for(int i=0;i<n;i++)cout<<ans[i]<<" ";
        cout<<endl;
        return;
    }
    for(int i=2;i<=n;i++){
        if(visit[i])continue;
        if(prime[val+i]==0)continue;
        visit[i] = 1;
        dfs(id+1,i);
        visit[i] = 0;
    }
}

signed main(){
    Orz;
    init();
    while(cin>>n){
        cnt+=1;
        memset(ans,0,sizeof(ans));
        memset(visit,0,sizeof(visit));
        cout<<"Case "<<cnt<<":"<<endl;
        dfs(0,1);
    }
}

```

### a142. 無刻度容器倒水問題

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define double long double
#define N 2000
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define rrep(i,l,r) for(int i=l;i<r;i++)
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int x,y,z;
int a[N][N];bool visit[N][N];

void solve(){
    cin>>x>>y>>z;
    memset(a,0x3f3f3f3f,sizeof(a));memset(visit,0,sizeof(visit));
    a[0][0] = 0;
    queue<pii>que;
    que.push({0,0});
    visit[0][0] = 1;
    
    bool f = 0;
    while(!que.empty()){
        pii cur = que.front();
        que.pop();
        int mmin = cur.x,mmax = cur.y;
        int dx[6] = {max(0,mmax-(y-mmin)),min(mmax+mmin,x),x,mmin,0,mmin};
        int dy[6] = {min(y,mmax+mmin),max(0,mmax-(x-mmin)),mmax,y,mmax,0};
        
        for(int i=0;i<6;i++){
            int nx = dx[i],ny = dy[i];
            if(nx==z||ny==z){
                f = 1;
                cout<<a[mmin][mmax]+1<<endl;
                return;
            }
            if(visit[nx][ny])continue;
            a[nx][ny] = a[mmin][mmax]+1;
            visit[nx][ny] = 1;
            que.push({nx,ny});
        }
    }
    if(!f)cout<<-1<<endl;
}

signed main(){
    Orz;
    int t;cin>>t;
    while(t--){
        solve();
    }
}

```

### a165. 最近點對問題

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define double long double
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define N 10002
#define INF 5e18
#define FOR(i,n) for(int i=0;i<n;i++)
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define pii pair<int,int>
#define x first
#define y second
#define pid pair<int,double>
#define pdi pair<double,int>
#define pdd pair<double,double>
using namespace std;
int n;
vector<pii> p,temp;

void init(){
    cout<<fixed<<setprecision(4);
    temp.clear();
    p.assign(n,{0,0});
}

bool cmp(pii a,pii b){
    return a.y < b.y;
}

double dis(pii a,pii b){
    double x1 = a.x-b.x,y1 = a.y-b.y;
    return sqrt(x1 * x1 + y1 * y1);
}

//區間[l,r]
double solve(int l,int r){
    if(l == r)return INF;
    int mid = (l+r)/2,mid_pos = p[mid].x;;
    double ans = min(solve(l,mid),solve(mid+1,r));
    
    temp.assign((r-l+1),{0,0});
    merge(
        p.begin() + l, p.begin() + mid + 1,
        p.begin() + mid + 1, p.begin() + r + 1,
        temp.begin(), cmp
    );
    rep(i, l, r)p[i] = temp[i-l];
    temp.clear();
    rep(i, l, r){
        if(abs(p[i].x - mid_pos) <= ans)
            temp.push_back(p[i]);
    }
    int len = temp.size();
    rep(i, 0, len-1){
        rep(j, i+1, len-1){
            ans = min(ans, dis(temp[i],temp[j]));
            if(abs(temp[i].y-temp[j].y) > ans)
                break;
        }
    }
    return ans;
}

signed main(){
    Orz;
    while(cin>>n){
        if(n==0)break;
        init();
        rep(i,0,n-1)cin>>p[i].x>>p[i].y;
        sort(p.begin(),p.end());
        double ans = solve(0,n-1);
        if(ans > 10000)cout<<"INFINITY"<<endl;
        else cout<<ans<<endl;
    }
}

```

## O. [基礎資料結構 II 及基礎演算法 II ] 動態規劃

### a143. 關鍵字搜尋模擬

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define double long double
#define N 105
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define rrep(i,l,r) for(int i=l;i<r;i++)
#define pii pair<int,int>
#define pif pair<int,float>
#define x first
#define y second
using namespace std;
int n,num_key = 0;
string key[N];
vector<pif> ans;

int com_string(string s1, string s2){
    s1 = "0"+s1;s2 = "0"+s2;
    int ans = 0,com[N][N];memset(com,0,sizeof(com));
    int len1 = s1.size(),len2 = s2.size();
    for(int i=1;i<len1;i++){
        for(int j=1;j<len2;j++){
            if(s1[i] == s2[j]){
                com[i][j] = com[i-1][j-1]+1;
                ans = max(ans,com[i][j]);
            }
        }
    }
    return ans;
}

bool cmp(pif a,pif b){
    if(a.y == b.y)return a.x < b.x;
    return a.y > b.y;
}

float max_num(string s){
    float ans = 0.0;
    for(int i=0;i<num_key;i++){
        ans = max(ans,(float)com_string(s,key[i])/key[i].size());
    }
    return ans;
}

signed main(){
    string s;getline(cin,s);
    stringstream ss(s);
    while(ss>>key[num_key++]);
    cin>>n;cin.ignore();
    while(n--){
        int doc_num;
        getline(cin,s);
        stringstream ss2(s);
        ss2>>doc_num;
        float score = 0.0;
        while(ss2 >> s)score += max_num(s);
        
        score = round(score*100)/100;
        ans.push_back({doc_num,score});
    }
    sort(ans.begin(),ans.end(),cmp);
    if(ans[0].y <= 0)cout<<"FALSE"<<endl;
    else for(auto i: ans)cout<<i.x<<" ";
}

```

### a144. 農作物採收問題

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define double long double
#define N 25
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define rrep(i,l,r) for(int i=l;i<r;i++)
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int n,num[N][N],dp[N][N];

void init(){
    memset(num,0,sizeof(num));
    memset(dp,0,sizeof(dp));
}

signed main(){
    init();
    cin>>n;
    rep(i,1,n)rep(j,1,n)cin>>num[i][j];
    
    for(int i=1;i<=n;i++){
        for(int j=1;j<=n;j++){
            num[i][j]+=num[i-1][j]+num[i][j-1]-num[i-1][j-1];
        }
    }
    int ans = 0;
    
    //左上(a,b)右下(x,y)
    rep(i,1,n)rep(j,1,n)rep(a,1,i)rep(b,1,j)
    ans = max(ans,num[i][j]-num[a-1][j]-num[i][b-1]+num[a-1][b-1]);
    
    cout<<ans<<endl;
}

```

### a145. 搬家規畫問題

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define double long double
#define N 1005
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define rrep(i,l,r) for(int i=l;i<r;i++)
#define pii pair<int,int>
#define x first
#define y second
using namespace std;

signed main(){
    string s;getline(cin,s);
    stringstream ss(s);
    
    int w[N],v[N],ind=0;
    while(ss>>w[ind++]);
    ind--;
    for(int i=0;i<ind;i++)cin>>v[i];
    int c;cin>>c;
    int dp[c+5];
    memset(dp,0,sizeof(dp));
    for(int i=0;i<ind;i++){
        for(int j=c;j>=w[i];j--){
            dp[j] = max(dp[j],dp[j-w[i]]+v[i]);
        }
    }
    cout<<dp[c]<<endl;
    
}

```

### a155. 雙子星塔

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define double long double
#define N 105
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define rrep(i,l,r) for(int i=l;i<r;i++)
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int n,m,dp[N][N],a[N],b[N],cnt = 0;

signed main(){
    
    while(cin>>n>>m){
        if(!n && !m)break;
        cnt+=1;
        cout<<"Twin Towers #"<<cnt<<endl;
        memset(dp,0,sizeof(dp));
        rep(i,1,n)cin>>a[i];
        rep(i,1,m)cin>>b[i];
        
        for(int i=1;i<=n;i++){
            for(int j=1;j<=m;j++){
                if(a[i]==b[j]){
                    dp[i][j] = dp[i-1][j-1]+1;
                }
                else dp[i][j] = max(dp[i-1][j],dp[i][j-1]);
            }
        }
        cout<<"Number of Tiles : ";
        cout<<dp[n][m]<<endl;
    }
}

```

## P. [基礎資料結構 II 及基礎演算法 II ] 樹狀圖形結構演算法

### a136. 元件測試排程問題

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define double long double
#define N 30
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define rrep(i,l,r) for(int i=l;i<r;i++)
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int n,m,ans[N];;
bool edge[N][N],is_root[N];

bool is_front(int u, int v){
    if(edge[u][v])return true;
    for(int i=0;i<n;i++){
        if(edge[u][i] && is_front(i,v))
            return true;
    }
    return false;
}

bool togological(){
    int deg[N],sum = 0,ind = 0;
    memset(deg,0,sizeof(deg));
    rep(i,0,n-1){
        rep(j,0,n-1){
            if(edge[i][j])deg[j] += 1;
        }
    }
    queue<int> que;
    rep(i,0,n-1)if(deg[i]==0){
        if(sum == 1)return false;
        sum += 1;
        que.push(i);
    }
    while(!que.empty()){
        int cur = que.front();sum = 0;
        ans[ind++] = cur;
        que.pop();
        for(int i=0;i<n;i++){
            if(edge[cur][i]){
                deg[i]--;
                if(deg[i] == 0){
                    if(sum == 1)return false;
                    sum += 1;
                    que.push(i);
                }
            }
        }
    }
    if(ind < n)return false;
    return true;
}


signed main(){
    Orz;
    cin>>n>>m;
    bool flag = 1;
    memset(edge,0,sizeof(edge));
    fill(is_root,is_root+N,1);
    for(int i=1;i<=m;i++){
        char a,b;cin>>a>>b;
        int from = a-'A',to = b-'A';
        is_root[to] = 0;
        edge[from][to] = 1;
        if(is_front(to,from)){
            flag = 0;
            cout<<"Order conflict after getting pair "<<i<<endl;
            break;
        }
        else if(togological()){
            flag = 0;
            cout<<"Determine the testing sequence after getting pair "<<i<<" : ";
            for(int i=0;i<n;i++)cout<<(char)(ans[i]+'A');
            cout<<endl;
            break;
        }
    }
    if(flag)cout<<"No answer"<<endl;
}

```

### a137. 勇者冒險

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define double long double
#define N 1005
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define rrep(i,l,r) for(int i=l;i<r;i++)
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int n,row,col,mp[N][N],dis[N][N];
int dx[4] = {0,-1,0,1},dy[4] = {1,0,-1,0};
bool visit[N][N];
typedef pair<int ,pair<int,int>> pp;

signed main(){
    Orz;
    cin>>row>>col;
    memset(dis,0,sizeof(dis));
    memset(visit,0,sizeof(visit));
    memset(mp,0,sizeof(mp));
    int s1,s2,e1,e2;cin>>s1>>s2>>e1>>e2;
    cin>>n;
    while(n--){
        int a,b,c;cin>>a>>b>>c;
        mp[a][b] = c;
    }

    //Dijkstra
    priority_queue<pp,vector<pp>,greater<pp>> pq;
    dis[s1][s2] = 0;
    pq.push({0,{s1,s2}});
    
    while(!pq.empty()){
        int rr = pq.top().y.x,cc = pq.top().y.y;
//        cout<<rr<<" "<<cc<<"  "<<dis[rr][cc]<<endl;
        pq.pop();
        for(int i=0;i<4;i++){
            int nx = rr+dx[i],ny = cc+dy[i];
            if(nx<0||ny<0||nx>=row||ny>=col)continue;
            if(nx == e1 && ny == e2){
                cout<<dis[rr][cc]<<endl;
                return 0;
            }
            if(mp[nx][ny] == 0)continue;
            if(visit[nx][ny])continue;
            dis[nx][ny] = max(dis[rr][cc],mp[nx][ny]);
            pq.push({dis[nx][ny],{nx,ny}});
            visit[nx][ny] = 1;
        }
    }
    return 0;
}

```

### a138. 最小花費的航空之旅

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define double long double
#define N 105
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define rrep(i,l,r) for(int i=l;i<r;i++)
#define pii pair<int,int>
#define pif pair<int,float>
#define x first
#define y second
using namespace std;
int n,k,visit[20];

struct Ticket{
    int id,cost;
    vector<int> next_city;
    //陣列ticket[i]存以i為起點
};
vector<Ticket> ticket[30];          //ticket[i] 起點為i的聯票

struct node{
    int cost,cur_pos,visit_pos;
    vector<int> used_ticket;
    //cur_pos現在所在位置、visit_pos在行程單上位置
};

struct cmp{
    bool operator()(node a,node b){
        return a.cost > b.cost;
    }
    //讓priority_queue可以pop出最小的cost的路徑
};
priority_queue<node,vector<node>,cmp> pq;

signed main(){
    Orz;
    cin>>n;
    rep(i,1,n){
        int cost,num,s;cin>>cost>>num>>s;
        vector<int> temp;
        rep(j,1,num-1){
            int k;cin>>k;
            temp.push_back(k);
        }
        ticket[s].push_back({i,cost,temp});
    }
    cin>>k;
    rep(i,1,k)cin>>visit[i];
    
    for(auto i : ticket[visit[1]]){         //行程第一個起點的聯票起點
        int p = 1;
        for(auto j : i.next_city){
            if(p < k && j == visit[p+1])p++;
            pq.push({i.cost,j,p,{i.id}});
//            cout<<"一開始聯票："<<i.id<<" "<<j<<endl;
        }
    }
    while(!pq.empty()){
        node cur = pq.top();
        pq.pop();
        if(cur.visit_pos == k){
            cout<<"Cost = "<<cur.cost<<", Tickets used: "
                <<cur.used_ticket[0];
            for(int i=1;i<cur.used_ticket.size();i++)
                cout<<", "<<cur.used_ticket[i];
            cout<<endl;
            break;
        }
        for(auto i : ticket[cur.cur_pos]){
            vector<int> vec(cur.used_ticket);
            vec.push_back(i.id);                //將現在使用的第i聯票推入
            int p = cur.visit_pos;
            for(auto j : i.next_city){
                if(p < k && j == visit[p+1])p++;
                pq.push({cur.cost+i.cost,j,p,vec});
            }
        }
    }
}

```

## Q. [題目解析] APCS 程式開發環境、題目解析

### a140. 物品堆疊

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define double long double
#define N 100005
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define rrep(i,l,r) for(int i=l;i<r;i++)
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int n;
pii p[N];

bool cmp(pii a, pii b){
    return a.x*b.y < a.y*b.x;
    //p[1~n]變成從上到下
}

signed main(){
    Orz;
    cin>>n;
    rep(i,0,n-1)cin>>p[i].x;
    rep(i,0,n-1)cin>>p[i].y;
    sort(p,p+n,cmp);
    int weight = 0,ans = 0;
    for(int i=0;i<n-1;i++){
        weight += p[i].x;
        ans += weight*p[i+1].y;
    }
    cout<<ans<<endl;
}

```

### a160. 線段覆蓋長度

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int long long
#define double long double
#define N 10005
#define Orz ios::sync_with_stdio(0),cin.tie(0)
#define INF 2e18
#define rep(i,l,r) for(int i=l;i<=r;i++)
#define rrep(i,l,r) for(int i=l;i<r;i++)
#define pii pair<int,int>
#define x first
#define y second
using namespace std;
int n;
pii a[N];

signed main(){
    Orz;
    while(cin>>n){
        rep(i,0,n-1)cin>>a[i].x>>a[i].y;
        sort(a,a+n);
        
        int sum = a[0].y - a[0].x,R = a[0].y;
        for(int i=1;i<n;i++){
            if(a[i].y > R){
                sum += (a[i].y-a[i].x)-(R-a[i].x)*((R-a[i].x)>0);
                R = a[i].y;
            }
        }
        cout<<sum<<endl;
    }
}

```
