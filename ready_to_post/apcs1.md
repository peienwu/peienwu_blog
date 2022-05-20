---
title: APCS CLASS PART 1
date: 2021-7-13
tags: 
    - APCS
    - 競賽筆記
categories: APCS檢定
mathjax: true
---

這是2021年暑期APCS課程的所有題目參考解答，共有34題。

## A. [基礎程式設計] 基礎C++程式設計複習

### a064/ 成績指標
<!--more-->
```cpp=
#include <bits/stdc++.h>
#define int long long
#define ios ios::sync_with_stdio(0)
using namespace std;

void solve(){
    int n,arr[25];cin>>n;
    for(int i=0;i<n;i++)cin>>arr[i];
    sort(arr,arr+n);
    cout<<arr[0];
    for(int i=1;i<n;i++)cout<<" "<<arr[i];
    cout<<endl;
    
    if(arr[0]>=60)cout<<"best case"<<endl;
    else cout<<*(lower_bound(arr,arr+n,60)-1)<<endl;
    
    if(arr[n-1]<60)cout<<"worst case"<<endl;
    else cout<<*(lower_bound(arr,arr+n,60))<<endl;
    
}

signed main(){
    int t;
    t = 1;
//    cin>>t;
    while(t--){
        solve();
    }
}

```

### a096/ 時間差計算

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0)
using namespace std;

signed main(){
    int a1,b1,c1,a2,b2,c2;
    scanf("%d:%d:%d",&a1,&b1,&c1);
    scanf("%d:%d:%d",&a2,&b2,&c2);
    int a = a1*3600+b1*60+c1,b = a2*3600+b2*60+c2;
    
    if(b-a>=0){
        int temp = b-a;
        printf("%02d:%02d:%02d",temp/3600,(temp%3600)/60,temp%60);
    }
    else{
        int temp = 86400+b-a;
        printf("%02d:%02d:%02d",temp/3600,(temp%3600)/60,temp%60);
    }
}

```

### a104/ 雪花片片

```cpp=
#include <bits/stdc++.h>
#define int long long
#define ios ios::sync_with_stdio(0)
using namespace std;
int n,product[100];

void solve(){
    memset(product,0,sizeof(product));
    product[0] = 1;
    for(int i=0;i<n;i++){
        for(int j=0;j<80;j++){
            product[j]=product[j]*4;
        }
        for(int j=0;j<80;j++){
            if(product[j]>=10){
                product[j+1] += product[j]/10;
                product[j] %=10;
            }
        }
    }
}

signed main(){
    cin>>n;
    solve();
    product[0]-=1;
    int temp=0;
    for(int i=80;i>=0;i--){
        product[i] += temp*10;
        temp = product[i]%3;
        product[i] /= 3;
    }
    
    int start = 80;
    for(int i=80;i>=0;i--)
        if(product[i]!=0){
            start = i;
            break;
        }
    for(int i=start;i>=0;i--)cout<<product[i];
    cout<<endl;
}

```

## B. [函式] 公用函式、自定函式

### a114/ 找出最小的完全平方數

```cpp=
#include <bits/stdc++.h>
#define ll long long
#define int ll
#define ios ios::sync_with_stdio(0)
#define FOR(i,n) for(int i=0;i<n;i++)
using namespace std;
int t;

void solve(){
    int k;cin>>k;
    int a = ceil(sqrt(pow(10,(k-1))));
    for(int i=a;;i++){
        string s = to_string(i*i);
        int len = s.size(),flag = 0;
        for(int i=0;i<len;i++){
            if((s[i]-'0')%2!=0){
                flag = 1;
                break;
            }
        }
        if(flag==0){
            cout<<i*i<<endl;
            break;
        }
    }
}

signed main(){
    ios;
    cin>>t;
    while(t--){
        solve();
    }
}

```

### a115/ 賓果遊戲

```cpp=
#include <bits/stdc++.h>
//#define int ll
//#define ll long long
#define N 16
#define mod 1000000007
#define FOR(i,n) for(int i=0;i<n;i++)
#define ios ios::sync_with_stdio(0)
using namespace std;
int t;

void solve(){
    int maze[10][10],cur[10][10],n;
    for(int i=0;i<5;i++){
        for(int j=0;j<5;j++){
            cin>>maze[i][j];
            cur[i][j] = maze[i][j];
        }
    }
    while(cin>>n && n!= -1){
        for(int i=0;i<5;i++){
            for(int j=0;j<5;j++){
                if(maze[i][j]==n)maze[i][j] = 0;
            }
        }
    }
    
    int ind = 1,num = -1;
    
    for(int i=0;i<5;i++){
        for(int j=0;j<5;j++){
            if(maze[i][j]!=0){
                maze[i][j] = 0;
                int k,ans = 0;
                for(k=0;k<5;k++)if(maze[k][j]!=0)break;
                if(k==5)ans+=1;
                for(k=0;k<5;k++)if(maze[i][k]!=0)break;
                if(k==5)ans+=1;
                
                if(i==j){
                    for(k=0;k<5;k++)if(maze[k][k]!=0)break;
                    if(k==5)ans+=1;
                }
                if(i+j==4){
                    int x = 4,y = 0;
                    for(k=0;k<5;k++){
                        if(maze[x][y]!=0)break;
                        else{
                            x--;y++;
                        }
                    }
                    if(k==5)ans++;
                }
                if(ans>num){
                    num = ans;
                    ind = cur[i][j];
                }
                else if(ans==num && ind>cur[i][j])ind = cur[i][j];
                maze[i][j] = cur[i][j];
            }
        }
    }
    cout<<ind<<endl;
}

signed main(){
    ios;
    t = 1;
    while(t--){
        solve();
    }
}

```

### a116/ 一起回家的日子

```cpp=
#include <bits/stdc++.h>
//#define ll long long
//#define int ll
#define ios ios::sync_with_stdio(0)
#define FOR(i,n) for(int i=0;i<n;i++)
using namespace std;
int t;

int gcd(int a,int b){
    if(a==b)return a;
    
    if(a<b)swap(a,b);
    while(b!=0){
        int c = a%b;
        a = b;
        b = c;
    }
    return a;
}

bool leap(int a){
    if((a%100!=0||a%400==0)&&(a%4==0))return 1;
    else return 0;
}

void solve(){
    int n,arr[100];cin>>n;
    for(int i=0;i<n;i++)cin>>arr[i];
    int ans = arr[0];
    for(int i=1;i<n;i++)ans=(ans*arr[i])/gcd(ans,arr[i]);
    
    int y,m,d;
    scanf("%d/%d/%d",&y,&m,&d);
    
    int month[15] = {0,31,28,31,30,31,30,31,31,30,31,30,31};
    d+=ans;
    
    while(d>month[m]){
        d-=month[m];
        if(m==12){
            y++;
            m = 1;
        }
        else{
            if(leap(y)&&m==2){
                d--;
            }
            m++;
        }
    }
    printf("%04d/%02d/%02d\n",y,m,d);
}

signed main(){
    t = 1;
    while(t--){
        solve();
    }
}

```

## C. [函式] 遞迴函式

### a117/ 三色河內塔

```cpp=
#include <bits/stdc++.h>
#define int long long
#define N 16
#define mod 1000000007
#define FOR(i,n) for(int i=0;i<n;i++)
#define ios ios::sync_with_stdio(0)
using namespace std;
int t;

void move(int n,char from,char to,char by){
    if(n<1)return;
    move(n-1,from,by,to);
    cout<<" ring "<<n<<" : "<<from<<" => "<<to<<endl;t++;
    move(n-1,by,to,from);
}

void honai(int n,char from,char to,char by){
    if(n<1)return;
    move(n-1,from,by,to);
    cout<<" ring "<<n<<" : "<<from<<" => "<<to<<endl;t++;
    honai(n-2,by,from,to);
}
signed main(){
    ios;
    int n;cin>>n;
    t = 0;
    honai(n,'A','C','B');
    cout<<"共需"<<t<<"個移動"<<endl;
}

```

### a118/ 指數2^k的四個自然數平方和之所有表示法

```cpp=
#include <bits/stdc++.h>
#define N 64000
#define mod 1000000007
#define FOR(i,n) for(int i=0;i<n;i++)
#define ios ios::sync_with_stdio(0)
using namespace std;
int num[5],sqr[N];
int solution = 0,n;

void init(){
    for(int i=0;i<N;i++)sqr[i] = i*i;
}

void pick(int remain,int id){
    if(id==4){
        int r = sqrt(remain);
        if(sqr[r]==remain){
            cout<<num[1]<<" "<<num[2]<<" "<<num[3]<<" "<<r<<endl;
            solution++;
        }
        return;
    }
    int lower = max(1,num[id-1]),upper = sqrt(remain/(5-id));
    
    for(int i=lower;i<=upper;i++){
        num[id]= i;
        pick(remain-sqr[i],id+1);
    }
}

signed main(){
    ios;
    init();
    cin>>n;
    pick(1<<n,1);
    if(solution==0)cout<<0<<endl;
}

```

### a157/ 費波那契數列

```cpp=
#include <bits/stdc++.h>
#define int long long
#define N 16
#define mod 1000000007
#define FOR(i,n) for(int i=0;i<n;i++)
#define ios ios::sync_with_stdio(0)
using namespace std;
int t;

signed main(){
    ios;
    int arr[40];arr[1] = 1;arr[2] = 1;
    for(int i=3;i<=30;i++){
        arr[i] = arr[i-1]+arr[i-2];
    }
    int n;
    while(cin>>n){
        cout<<arr[n]<<endl;
    }
}

```

### a158/ F91

```cpp=
#include <bits/stdc++.h>
#define int long long
#define N 16
#define mod 1000000007
#define FOR(i,n) for(int i=0;i<n;i++)
#define ios ios::sync_with_stdio(0)
using namespace std;
int t;

signed main(){
    ios;
    int arr[40];arr[1] = 1;arr[2] = 1;
    for(int i=3;i<=30;i++){
        arr[i] = arr[i-1]+arr[i-2];
    }
    int n;
    while(cin>>n){
        cout<<arr[n]<<endl;
    }
}

```

## D. [基本資料型態] 指標、多維陣列

### a105. 爺爺種樹

```cpp=
#include <bits/stdc++.h>
#define N 505
#define mod 1000000007
#define FOR(i,n) for(int i=0;i<n;i++)
#define ios ios::sync_with_stdio(0)
using namespace std;


signed main(){
    ios;
    int n,m,t;cin>>n>>m>>t;
    bool arr[N][N];
    memset(arr,0,sizeof(arr));
    
    while(t--){
        int r1,r2,c1,c2;cin>>r1>>c1>>r2>>c2;
        int dr = r2-r1,dc = c2-c1;
        if(dr>0)dr = 1;else dr = -1;
        if(dc>0)dc = 1;else dc = -1;
        
        if(r1==r2){
            for(int i=c1;i!=c2;i+=dc)arr[r1][i] = 1;
            arr[r1][c2] = 1;
        }
        else if(c1==c2){
            for(int i=r1;i!=r2;i+=dr)arr[i][c1] = 1;
            arr[r2][c1] = 1;
        }
        else{
            for(int i=r1,j=c1;i!=r2;i+=dr,j+=dc){
                arr[i][j] = 1;
                arr[r2][c2] = 1;
            }
        }
    }
    int ans = 0;
    for(int i=1;i<=n;i++){
        for(int j=1;j<=m;j++){
            if(arr[i][j])ans+=1;
        }
    }
    cout<<ans<<endl;
}

```

### a106. 賓果遊戲

```cpp=
#include <bits/stdc++.h>
#define N 105
#define mod 1000000007
#define FOR(i,n) for(int i=0;i<n;i++)
#define ios ios::sync_with_stdio(0)
using namespace std;
char name[N],line[N][10];
pair<int,int> mp[N][N];

bool check(int people,int number){
    int r = mp[people][number].first,c = mp[people][number].second;
    line[people][r]+=1;
    line[people][c+4]+=1;
    if(line[people][r]==4)return 1;
    if(line[people][c+4]==4)return 1;
    
    if(r+c==3){
        line[people][8]+=1;
        if(line[people][8]==4)return 1;
    }
    if(r==c){
        line[people][9]+=1;
        if(line[people][9]==4)return 1;
    }
    return 0;
}

signed main(){
    ios;
    int n;
    char cha;
    cin>>cha>>n;
    memset(line,0,sizeof(line));
    
    for(int i=0;i<n;i++){
        cin>>name[i];
        for(int j=0;j<16;j++){
            int temp;cin>>temp;
            mp[i][temp].first = j/4;
            mp[i][temp].second = j%4;
        }
    }
    int cnt = 16,flag = 0;
    cin>>cha;
    while(cnt--){
        int temp;cin>>temp;
        cout<<temp<<" ";
        for(int i=0;i<n;i++){
            if(check(i,temp)){
                cout<<name[i]<<" ";
                flag = 1;
            }
        }
        if(flag)break;
    }
}

```

### a107. 加密解密

```cpp=
#include <bits/stdc++.h>
#define N 105
#define mod 1000000007
#define FOR(i,n) for(int i=0;i<n;i++)
#define ios ios::sync_with_stdio(0)
using namespace std;
string a = "abcdefghijklmnoprstuvwxyz";
string b = "EXAMPLBCDFGHIJKNORSTUVWYZ";

signed main(){
    ios;
    int n;string s;
    cin>>n;cin.ignore();
    getline(cin,s);
    reverse(s.begin(),s.end());
    string origin = b ,key = a;
    if(s[0]>='a'&& s[0]<='z')swap(origin,key);
    
    for(int i=0;i<n;i+=2){
        int p1 = origin.find(s[i]),p2 = origin.find(s[i+1]);
        cout<<key[5*(p1/5)+(p2%5)]<<key[5*(p2/5)+(p1%5)];
    }
    cout<<endl;
}

```

### a147/ 促銷活動

```cpp=
#include <iostream>
using namespace std;

void discount(double &a,double &b){
    if(a==b){
        b = 0.5*b;
    }
}
int main(){
    double p1, p2;
    cout << "Original price:" << endl;
    cin >> p1 >> p2;
    discount(p1,p2);
    cout << "Price after discount:" << endl;
    cout << p1 << " " << p2 << endl;
    return 0;
}

```

### a159/ 錯誤更正

```cpp=
#include <bits/stdc++.h>
#define N 105
#define mod 1000000007
#define FOR(i,n) for(int i=0;i<n;i++)
#define ios ios::sync_with_stdio(0)
using namespace std;


signed main(){
    ios;
    int n;
    while(cin>>n && n!=0){
        int row_sum[N],col_sum[N],arr[N][N];
        memset(row_sum,0,sizeof(row_sum));
        memset(col_sum,0,sizeof(col_sum));
        memset(arr,0,sizeof(arr));
        
        for(int i=1;i<=n;i++){
            for(int j=1;j<=n;j++){
                cin>>arr[i][j];
                row_sum[i]+=arr[i][j];
                col_sum[j]+=arr[i][j];
            }
        }
        int rcnt = 0,ccnt = 0;
        int rind = 0,cind = 0;
        
        for(int i=1;i<=n;i++){
            if(row_sum[i]%2){
                rcnt += 1;
                rind = i;
            }
            if(col_sum[i]%2){
                ccnt += 1;
                cind = i;
            }
        }
        if(rcnt==0 && ccnt==0)cout<<"OK"<<endl;
        else if(rcnt==1 && ccnt==1)cout<<"Change bit ("<<rind<<","<<cind<<")"<<endl;
        else cout<<"Corrupt"<<endl;
    }
}

```

## E. [基本資料型態] 字串

### a065. 秘密差

```cpp=
#include <bits/stdc++.h>
using namespace std;

int main(){
    string s;
    while(cin>>s){
        int ans = 0,len = s.size(),p=1;
        for(int i=0;i<len;i++){
            ans+=(s[i]-'0')*p;
            p *=-1;
        }
        cout<<abs(ans)<<endl;
    }
}

```

### a067. ROT13

```cpp=
#include <bits/stdc++.h>
#define ld long double
using namespace std;

int main(){
    string s;
    getline(cin,s);
    for(auto p:s){
        if(isalpha(p)){
            if(isupper(p))
                cout<<(char)((p-'A'+13)%26+'A');
            else
                cout<<(char)((p-'a'+13)%26+'a');
        }
        else{
            cout<<p;
        }
    }
}

```

### a108. 計算字串間隔距離

```cpp=
#include <bits/stdc++.h>
using namespace std;
map<string,queue<pair<string,int>>> mp;
//地點、動物名、數量
int main(){
    string s;
    char ch;
    cin>>s>>ch;
    ch = tolower(ch);
    int len = s.size(),a1;
    for(int i=0;i<len;i++){
        if(tolower(s[i])==ch){
            a1 = i;
            break;
        }
    }
    int a2;
    for(int i=a1+1;i<len;i++){
        if(tolower(s[i])==ch){
            a2 = i;
            cout<<a2-a1<<" ";
            a1 = a2;
        }
    }
    cout<<endl;
}

```

### a109. 跑長編碼與資料壓縮

```cpp=
#include <bits/stdc++.h>
#define ld long double
using namespace std;

int main(){
    int n,origin,after,times;
    string s,t,binary[8]={"000","001","010","011","100","101","110","111"};
    char ch;
    cin>>n;
    cin.ignore();
    while(n--){
        getline(cin,s);
        t="";
        origin = s.size();
        after = 0;
        ch = s[0];
        times = 1;
        int len = s.size();
        for(int i=1;i<=len;i++){
            if(i!=len && s[i]!='0'&&s[i]!='1'){
                after = -1;
                break;
            }
            if(s[i]!=ch){
                t+=ch;
                t+=binary[times];
                t+=" ";
                times=1;
                ch = s[i];
                after+=4;
            }
            else{
                times++;
                if(times==7){
                    t+=ch;
                    t+=binary[7];
                    t+=" ";
                    times=1;
                    after +=4;
                    ch = s[i+1];
                    i++;
                }
            }
        }
        if(after==-1)cout<<after<<endl;
        else cout<<t<<fixed<<setprecision(0)<<(double)after*100/origin<<"%"<<endl;
    }
}

```

### a110. 棒球遊戲

```cpp=
#include <bits/stdc++.h>
using namespace std;
int num_of_out,d[3] = {0};

int transfer(string a){
    if(a=="SO"||a=="FO"||a=="GO")return 0;
    else if(a=="1B")return 1;
    else if(a=="2B")return 2;
    else if(a=="3B")return 3;
    else return 4;
}

int win(int c){
    int s = 0;
    if(c!=4){
        for(int i=0;i<c;i++){
            if(d[2-i])s++;
        }
        for(int i=0;i<3-c;i++){
            d[2-i] = d[2-c-i];
        }
    }
    if(c==4){
        for(int i=0;i<3;i++)if(d[i])s++;
        s++;
    }
    else d[c-1] = 1;
    for(int i=0;i<c-1;i++)d[i] = 0;
    return s;
}

int main(){
    int data[9][5];
    for(int i=0;i<9;i++){
        int temp;
        cin>>temp;
        for(int k=0;k<temp;k++){
            string a;
            cin>>a;
            data[i][k] = transfer(a);
        }
    }
    cin>>num_of_out;
    int now_out = 0,ans = 0;
    for(int k=0;k<5;k++){
        for(int i=0;i<9;i++){
            if(now_out>=num_of_out)break;
            if(data[i][k]){
                ans+=win(data[i][k]);
            }
            else{
                now_out++;
                if(now_out%3==0){
                    for(int i =0;i<3;i++)d[i] = 0;
                }
            }
        }
    }
    cout<<ans<<endl;
}

```

## F. [基本資料型態] 結構

### a111. 排隊

```cpp=
#include <bits/stdc++.h>
using namespace std;

int main(){
    int n;cin>>n;
    int t[1005][3],line=0,ans=0,cur=0;
    
    cin>>t[0][0]>>t[0][1];
    t[0][2] = t[0][0]+t[0][1];
    for(int i=1;i<n;i++){
        cin>>t[i][0]>>t[i][1];
        if(t[i][0]<t[i-1][2])t[i][2] = t[i-1][2]+t[i][1];
        else t[i][2] = t[i][0]+t[i][1];
        
        if(t[i][0]>=t[cur][2]){
            ans = max(ans,line);
            while(t[i][0]>=t[cur][2]){
                if(cur==i)break;
                line--;
                cur++;
            }
        }
        if(t[i][0]<t[cur][2])line++;
    }
    ans = max(ans,line);
    cout<<ans<<endl;
}

```

### a112. 動物數量統計

```cpp=
#include <bits/stdc++.h>
using namespace std;
map<string,queue<pair<string,int>>> mp;
//地點、動物名、數量
int main(){
    int n;cin>>n;
    vector<string> p;
    while(n--){
        string animal,pos;
        int qu,ind;
        cin>>animal>>qu>>pos;
        ind = find(p.begin(),p.end(),pos)-p.begin();
        if(ind==p.size())p.push_back(pos);
        mp[pos].push({animal,qu});
    }
    for(auto i:p){
        cout<<i<<":";
        map<string,int> sta;
        vector<string> almp;
        while(!mp[i].empty()){
            string an = mp[i].front().first;
            int qu = mp[i].front().second;
            mp[i].pop();
            int ind = find(almp.begin(),almp.end(),an)-almp.begin();
            if(ind==almp.size())almp.push_back(an);
            sta[an]+=qu;
        }
        bool c = 0;
        for(auto j:almp){
            if(c)cout<<",";
            cout<<j<<" "<<sta[j];
            c = 1;
        }
        cout<<endl;
    }
}

```

### a113. 99遊戲

```cpp=
#include <bits/stdc++.h>
using namespace std;

map<int,queue<string>> mp;

int main(){
    for(int i=0;i<4;i++){
        char ch;cin>>ch;
        for(int j=0;j<13;j++){
            string s;cin>>s;
            mp[ch-'A'].push(s);
        }
    }
    int id = 0,dir=1,sum=0;
    while(sum<=99){
        string card = mp[id].front();
        if(card=="A")sum=0;
        else if(card=="4")dir*=-1;
        else if(card=="5");
        else if(card=="10"){
            sum+=10;
            if(sum>99)sum-=20;
        }
        else if(card=="J");
        else if(card=="Q"){
            sum+=20;
            if(sum>99)sum-=40;
        }
        else if(card=="K")sum=99;
        else{
            sum+=(card[0]-'0');
            if(sum>99){
                cout<<char('A'+id)<<endl<<mp[id].size()-1<<endl;
                break;
            }
        }
        mp[id].pop();
        if(mp[id].empty()){
            cout<<char('A'+id)<<endl<<sum<<endl;
            break;
        }
        id = (id+dir+4)%4;
//        cout<<sum<<endl;
    }
}

```

### a150. 多邊形面積

```cpp=
#include <bits/stdc++.h>
using namespace std;

struct point{
    double x;
    double y;
};

int main(){
    int n;
    cin>>n;
    point p[n];
    
    for(int i=0;i<n;i++)cin>>p[i].x>>p[i].y;
    double a = 0.0;
    for(int i=0;i<n;i++){
        if(i==n-1){
            a+=(p[i].x)*(p[0].y)-(p[0].x)*(p[i].y);
        }
        else{
            a+=(p[i].x)*(p[i+1].y)-(p[i+1].x)*(p[i].y);
        }
    }
    a = abs(a/2);
    cout<<fixed<<setprecision(2)<<a<<endl;
}

```
