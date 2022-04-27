---
title: CSES:Introductory Problems
date: 2021-9-30
tags: 
    - CSES
categories:
    - CSES
mathjax: true
---

[CSES](https://cses.fi/) 解題紀錄。

[My Account](https://cses.fi/user/84243)
[Thanksone帳號](https://cses.fi/user/84265)
[CSES Plan](https://hackmd.io/@thanksone/HyzF2w_Ht)

## Introductory Problems

### Weird Algorithm
[題目連結](https://cses.fi/problemset/task/1068)

<!--more-->

```cpp=
#include <bits/stdc++.h>
using namespace std;
int n;
 
signed main(){
    cin>>n;
    while(n != 1){
        cout<<n<<" ";
        if(n % 2 == 1)n = 3 * n + 1;
        else n = n/2;
    }
    cout<<1<<"\n";
}
```
### Missing Number
[題目連結](https://cses.fi/problemset/task/1083)

```cpp=
#include <bits/stdc++.h>
#define int long long
using namespace std;
int n;
 
signed main(){
    cin>>n;
    int sum = n*(n+1)/2;
    for(int i=0;i<n-1;i++){
        int temp;cin>>temp;
        sum -= temp;
    }
    cout<<sum<<endl;
}
```

### Repetitions
[題目連結](https://cses.fi/problemset/task/1069)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
 
signed main(){
    IOS;
    string s;cin>>s;
    int len = s.size();
    int ans = 1,cur = 1;
    for(int i = 1;i < len;i++){
        if(s[i] == s[i-1])cur++;
        else cur = 1;
        ans = max(ans,cur);
    }
    cout<<ans<<"\n";
}
```
### Increasing Array
[題目連結](https://cses.fi/problemset/task/1094)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define N 200005
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n,arr[N];
 
signed main(){
    IOS;
    cin>>n;
    for(int i=0;i<n;i++)cin>>arr[i];
    
    int ans = 0;
    for(int i=1;i<n;i++){
        if(arr[i] < arr[i-1]){
            ans += arr[i-1] - arr[i];
            arr[i] = arr[i-1];
        }
    }
    cout<<ans<<"\n";
}
```


### Permutations
[題目連結](https://cses.fi/problemset/task/1070)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define N 1000005
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n;
 
signed main(){
    IOS;
    cin>>n;
    bool vis[N];memset(vis,0,sizeof(vis));
    if(n == 2 || n == 3)cout<<"NO SOLUTION"<<endl;
    else if(n == 4)cout<<"2 4 1 3 ";
    else{
        for(int i=1;i<=n;i+=2){
            cout<<i<<" ";
            vis[i] = 1;
        }
        for(int i=1;i<=n;i++){
            if(vis[i] == 0)cout<<i<<" ";
        }
    }
    cout<<endl;
}
```

### Number Spiral
[題目連結](https://cses.fi/problemset/task/1071)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define N 1000005
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n;
 
signed main(){
    IOS;
    cin>>n;
    while(n--){
        int x,y;cin>>x>>y;
        int ly = max(x,y)-1,st = ly*ly;
        if(!(ly % 2)){
            if(y < ly+1)cout<<st+y<<endl;
            else cout<<(st+ly+1)+(ly+1-x)<<endl;
        }
        else{
            if(x < ly+1)cout<<st+x<<endl;
            else cout<<(st+ly+1)+(ly+1-y)<<endl;
        }
    }
}
```

### Two Knights

[題目連結](https://cses.fi/problemset/task/1072)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define N 10005
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n,dp[N];
 
void build(){
    memset(dp,0,sizeof(dp));
    for(int i=2;i<=10000;i++){
        int sum = (i*i)*(i*i-1)/2 - 4*(i-1)*(i-2);
        dp[i] = sum;
    }
}
 
signed main(){
    IOS;
    build();
    cin>>n;
    for(int i=1;i<=n;i++){
        cout<<dp[i]<<"\n";
    }
}
```

### Two Sets

[題目連結](https://cses.fi/problemset/task/1092)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define N 1000005
using namespace std;
int n;

void solve(){
    int total = n*(n+1)/2,sum = 0,cnt = 0;
    bool vis[N];memset(vis,0,sizeof(vis));
    if(total & 1){
        cout<<"NO"<<"\n";
        return;
    }
    cout<<"YES"<<"\n";
    for(int i=n;i>=1;i--){
        int next = sum + i;
        if(next <= total/2){
            sum += i;
            cnt++;
            vis[i] = 1;
        }
    }
    cout<<cnt<<"\n";
    for(int i=1;i<=n;i++){
        if(vis[i])cout<<i<<" ";
    }
    cout<<"\n"<<n - cnt<<"\n";
    for(int i=1;i<=n;i++){
        if(!vis[i])cout<<i<<" ";
    }
    cout<<"\n";
}

signed main(){
    cin>>n;
    solve();
    return 0;
}
```

### Bit Strings

[題目連結](https://cses.fi/problemset/task/1617)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define N 1000005
#define mod 1000000007
using namespace std;
int n;

void solve(){
    int ans = 1;
    for(int i=0;i<n;i++){
        ans *= 2;
        ans = ans % mod;
    }
    cout<<ans<<"\n";
}

signed main(){
    cin>>n;
    solve();
    return 0;
}
```

### Trailing Zeros

[題目連結](https://cses.fi/problemset/task/1618)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define N 1000005
#define mod 1000000007
using namespace std;
int n;

void solve(){
    int f = 5,ans = 0;
    for(int i=0;i<12;i++){
        ans += n / f;
        f *= 5;
    }
    cout<<ans<<"\n";
}

signed main(){
    cin>>n;
    solve();
    return 0;
}
```

### Coin Piles

[題目連結](https://cses.fi/problemset/task/1754)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define N 1000005
#define mod 1000000007
using namespace std;
int n;

void solve(){
    int a,b;cin>>a>>b;
    if(a > b)swap(a,b);
    if(b - a > a){
        cout<<"NO"<<"\n";
        return;
    }
    a = a % 3;b = b % 3;
    if(a > b)swap(a,b);
    if(a == 1 && b == 2)cout<<"YES"<<"\n";
    else if(a == 0 && b == 0)cout<<"YES"<<"\n";
    else cout<<"NO"<<"\n";
}

signed main(){
    int t;cin>>t;
    while(t--){
        solve();
    }
    return 0;
}
```

### Palindrome Reorder

[題目連結](https://cses.fi/problemset/task/1755)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define N 1000005
using namespace std;
int n;
 
signed main(){
    string str,ans;cin>>str;
    n = str.size();
    int num[N],cnt = 0,cha = -1;memset(num,0,sizeof(num));
    for(int i = 0;i < n;i++){
        num[str[i] - 'A'] += 1;
    }
    for(int i=0;i<26;i++){
        if(num[i] & 1){
            cnt++;cha = i;
        }
    }
    ans.resize(n);
    if(cnt > 1)cout<<"NO SOLUTION\n";
    else if(cnt == 1 && !(n&1))cout<<"NO SOLUTION\n";
    else if(cnt < 1 && n&1)cout<<"NO SOLUTION\n";
    else{
        if(n & 1){
            ans[n/2] = 'A'+cha;
            num[cha]--;
        }
        int l = 0,r = n-1;
        for(int i=0;i<26;i++){
            while(num[i]){
                ans[l++] = i + 'A';
                ans[r--] = i + 'A';
                num[i] -= 2;
            }
        }
        cout<<ans<<"\n";
    }
}
```

### Gray Code

[題目連結](https://cses.fi/problemset/task/2205)

```cpp=
#include <bits/stdc++.h>
#define pb push_back
#define IOS ios::sync_with_stdio(0),cin.tie(0)
#define int long long
using namespace std;
int n;
vector<string> str;
 
void solve(){
    str.pb("0");str.pb("1");
    for(int i=0;i<n-1;i++){
        vector<string> temp1 = str,temp2 = str;
        reverse(temp2.begin(),temp2.end());
        str.clear();
        int len = temp1.size();
        for(int i=0;i<len;i++)temp1[i] = "0" + temp1[i];
        for(int i=0;i<len;i++)temp2[i] = "1" + temp2[i];
        str.insert(str.begin(),temp1.begin(),temp1.end());
        str.insert(str.end(),temp2.begin(),temp2.end());
    }
    for(auto i : str)cout<<i<<"\n";
}

signed main(){
    IOS;
    cin>>n;
    solve();
    return 0;
}
```

### Tower of Hanoi

[題目連結](https://cses.fi/problemset/task/2165)

```cpp=
#include <bits/stdc++.h>
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n;

void solve(int from,int to,int by,int n){
    if(n < 1)return;
    solve(from,by,to,n-1);
    cout<<from<<" "<<to<<"\n";
    solve(by,to,from,n-1);
}

signed main(){
    IOS;
    cin>>n;
    cout<< (1<<n)-1 <<"\n";
    solve(1,3,2,n);
    return 0;
}
```

### Creating Strings

[題目連結](https://cses.fi/problemset/task/1622)

```cpp=
#include <bits/stdc++.h>
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n;

signed main(){
    IOS;
    string s;cin>>s;
    int len = s.size(),num[30];
    memset(num,0,sizeof(num));
    for(int i=0;i<len;i++){
        num[s[i] - 'a'] += 1;
    }
    string str;
    for(int i = 0;i < 26;i++){
        while(num[i]){
            str += (char)(i + 'a');
            num[i]--;
        }
    }
    vector<string> ans;
    do{
        ans.push_back(str);
    }while(next_permutation(str.begin(),str.end()));
    
    cout<<ans.size()<<"\n";
    for(auto i : ans)cout<<i<<"\n";
    return 0;
}
```

### Apple Division

[題目連結](https://cses.fi/problemset/task/1623)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n,m = 0,arr[21];

void dp(){
    int ans = LONG_LONG_MAX;
    for(int i=0;i<1<<n;i++){
        int sum = 0;
        for(int j=0;j<n;j++){
            if(1<<j & i)sum += arr[j];
        }
        ans = min(ans,abs(m - sum - sum));
    }
    cout<<ans<<"\n";
}

signed main(){
    IOS;
    cin>>n;
    for(int i=0;i<n;i++){
        cin>>arr[i];
        m += arr[i];
    }
    dp();
}
```

### Chessboard and Queens

[題目連結](https://cses.fi/problemset/task/1624)

```cpp=
#include <bits/stdc++.h>
#define int long long
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
bool maze[10][10],vis[10][10];
int ans = 0;

bool check(int x,int y){
    for(int i=1;i<=8;i++){
        for(int j=1;j<=8;j++){
            if(vis[i][j]){
                if(abs(i - x) == abs(j - y))return false;
                if(i == x || j == y)return false;
            }
        }
    }
    return true;
}

void dfs(int cur){
    if(cur > 8){
        ans++;
        return;
    }
    for(int i=1;i<=8;i++){
        if(check(cur,i) && maze[cur][i]){
            vis[cur][i] = 1;
            dfs(cur+1);
            vis[cur][i] = 0;
        }
    }
}

signed main(){
    IOS;
    memset(maze,0,sizeof(maze));
    memset(vis,0,sizeof(vis));
    for(int i=1;i<=8;i++){
        string s;getline(cin,s);
        for(int j=1;j<=8;j++){
            if(s[j-1] == '.')maze[i][j] = 1;
        }
    }
    dfs(1);
    cout<<ans<<"\n";
}
```

### Digit Queries

[題目連結](https://cses.fi/problemset/task/2431)

```cpp=
#include <bits/stdc++.h>
#define N 20
#define int long long
#define IOS ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n,pref[N],p[20];

void init(){
    p[0] = 1;
    for(int i=1;i<19;i++){
        p[i] = p[i-1] * 10;
    }
    pref[1] = 9;
    for(int i=2;i<18;i++){
        pref[i] = p[i-1] * 9 * i;
    }
}

void solve(){
    int ind = 1;
    while(n > pref[ind])n -= pref[ind++];
    n--;                    //0-BASE
    int x = n / ind;        //0-BASE
    int y = n % ind;        //0-BASE
    int ans = p[ind-1] + x;
    string temp = to_string(ans);
    cout<<temp[y]<<"\n";
}

signed main(){
    IOS;
    init();
    int t;cin>>t;
    while(t--){
        cin>>n;
        solve();
    }
}
```

### Grid Paths

[題目連結](https://cses.fi/problemset/task/1625)

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
string str;
int dx[4] = {1,0,-1,0},dy[4] = {0,1,0,-1};
bool vis[9][9];
int ans = 0;
 
void solve(int x,int y,int s){
    if(x < 1 || x > 7 || y < 1 || y > 7 || vis[x][y])return;
    if(x == 1 && y == 7 && s < 48)return;
    if(vis[x-1][y] && vis[x+1][y] && !vis[x][y+1] && !vis[x][y-1])return;
    if(!vis[x-1][y] && !vis[x+1][y] && vis[x][y+1] && vis[x][y-1])return;
    if(s == 48){
        ans++;
        return;
    }
    vis[x][y] = 1;
    int ans = 0;
    if(str[s] == 'L')solve(x - 1, y, s + 1);
    if(str[s] == 'R')solve(x + 1, y, s + 1);
    if(str[s] == 'U')solve(x, y - 1, s + 1);
    if(str[s] == 'D')solve(x, y + 1, s + 1);
    if(str[s] == '?'){
        for(int i = 0;i < 4;i++){
            int nx = x + dx[i],ny = y + dy[i];
            solve(nx,ny,s+1);
        }
    }
    vis[x][y] = 0;
}
 
signed main(){
    IOS;
    cin>>str;
    memset(vis,0,sizeof(vis));
    for(int i=1;i<=7;i++){
        vis[i][0] = 1;
        vis[8][i] = 1;
        vis[i][8] = 1;
        vis[0][i] = 1;
    }
    solve(1,1,0);
    cout<<ans<<"\n";
}
```
