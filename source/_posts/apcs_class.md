---
title: APCS CLASS
date: 2021-7-30
tags: 
    - APCS
    - 競賽筆記
categories: APCS檢定
mathjax: true
---

這是2021年暑期APCS課程的所有題目參考解答，共有34題。

## A. [基礎程式設計] 基礎C++程式設計複習

### a064/ 成績指標

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
