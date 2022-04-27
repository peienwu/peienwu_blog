---
title: 資芽第五週：貪心算法
date: 2021-4-3
tags: 
    - 資芽筆記
    - 貪心
categories: 資訊之芽筆記
mathjax: true
---

這一週主題是**Greedy**，其實貪心法的題目要難可以到很難，有點像一個抽象的想法，需要透過反覆的實作與練習（顯然我缺乏），來更容易的想到題目的解法！

## 上課內容

影片的內容主要是在證明greedy 的正確性，超複雜的數學證明（又是數學證明！）
原本以為上課會延續證明的內容，沒想到完全沒有講到證明的內容，都是在講解例題（也不錯，有很多題需要慢慢理解）
所以列了上課講過的例題，一定要搞懂！

<!--more-->

![](https://i.imgur.com/l9YwEbU.png)
絕對不要輕忽 Greedy 啊，想法看似單純，要想出解法，還是需要經驗的！

## 上機作業

### Add all(UVA)

[題目連結](https://neoj.sprout.tw/problem/70/)
這一題可以輕鬆觀察出規律：數字越早被相加，被加總的次數就會越多
因此，維護一個priority_queue，每次pop出最小的兩個數字再push 回去，可以保證總和一定會是最小的

```cpp=
#include <bits/stdc++.h>
#define int long long
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n;

signed main(){
    ios;
    priority_queue<int, vector<int>, greater<int> >qq;
    while(cin>>n && n!=0){
        for(int i=0;i<n;i++){
            int temp;cin>>temp;
            qq.push(temp);
        }
        int ans = 0;
        while(qq.size()>1){
            int a,b;
            a = qq.top();qq.pop();
            b = qq.top();qq.pop();
            qq.push(a+b);
            ans+=(a+b);
        }
        cout<<ans<<endl;
        qq.pop();
    }
}

```

### 円円攻略黃河(TIOJ)

[題目連結](https://neoj.sprout.tw/problem/74/)
這一題要求最長的長短相間的序列長度，雖然知道是Greedy ，但第一時間想不到具體的作法
卡了有一點久，總共嘗試了三次，前兩次以為對，但WA 後還是發現其中的Bug

1. 從第一個數字開始，找到下一個比自己小的數字，再找比那一個大的，重複幾次直到結束
{% note info %}
問題在於，第一個數字可能很小，導致接下來的序列長度很短
{% endnote %}
2. 找到第一個「下一個數比自己小」的數字，接著重複第1點的步驟
{% note info %}
因為要保證序列是最長的，會希望在找比自己大或小的數字時可以盡量最大或最小，才可以確保所有數字能有機會被包含進去
{% endnote %}
3. 對每一個數字如果要找大於自己的，就一直繼續往上找直到最大，如果要找最小的也是一樣
{% note info %}
以 *5 1 2 4 5 3 6* 來說，就是從5開始，接下來是1（最小了），接下來往上找，可以找到2, 4, 5，而為了盡量讓數字大才能包到接下來的數字（如果選2下一個就不能是3），所以選5，再來就是3 跟6 。
所以序列為： *5 1 5 3 6* ， 長度為5
{% endnote %}
想說應該要AC 了，沒想到沒有memset 所以吃了一個WA ！

```cpp=
#include <bits/stdc++.h>
#define int long long
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int t;

signed main(){
    ios;
    cin>>t;
    int n,arr[1000005];
    while(t--){
        cin>>n;
        memset(arr,0,sizeof(arr));
        for(int i=0;i<n;i++)cin>>arr[i];
        int height = 0, ind = 0, ans = 1;//0->down 1->up
        while(ind<n && arr[ind]<=arr[ind+1])ind++;
        
        while(ind<n){
            if(height == 0 && arr[ind]!=arr[ind+1]){
                while(arr[ind]>=arr[ind+1])ind++;
                height = 1;
            }
            else if(height == 1 && arr[ind]!=arr[ind+1]){
                while(arr[ind]<=arr[ind+1])ind++;
                height = 0;
                ans+=2;
            }
            ind++;//如果有連續相同數字需要加
        }
        cout<<ans<<endl;
    }
}
```

### 誰先晚餐

[題目連結](https://neoj.sprout.tw/problem/89/)
按照Greegy 的想法，吃最慢的人要最先吃，吃最快的人要後吃，才能讓整個時間盡量的縮短
這一題之前學長講解過，所以自然是比較簡單。But! 要怎麼證明這樣可以得到最佳解呢～留到手寫作業！

```cpp=
#include <bits/stdc++.h>
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int n;

struct student{
    int eat,cook;
}people[10005];

bool comp(student a,student b){
    return a.eat > b.eat;
}
int main(){
    ios;
    while(cin>>n){
        if(n==0)break;
        for(int i=0;i<n;i++){
            cin>>people[i].cook>>people[i].eat;
        }
        sort(people, people+n, comp);
        int ans = 0,cook = 0;
        for(int i=0;i<n;i++){
            cook+=people[i].cook;//煮飯時間一定總數不會改變
            int leave = cook+people[i].eat;
            ans = max(ans, leave);
        }
        cout<<ans<<endl;
    }
}
```

### 包裝禮物

[題目連結](https://neoj.sprout.tw/problem/78/)
一直吃WA ，吃到懷疑人生哎～
想法：由大方塊到小方塊，盡量填滿，如果過程中大小為4的方塊變成負的，可以先欠著，直到最後一次由大小為1的來補，這樣可以確保每一格方塊不是被1*1的填滿就是剩下最少的空格

```cpp=
#include <bits/stdc++.h>
#define int long long
#define ios ios::sync_with_stdio(0),cin.tie(0)
using namespace std;
int a,b,c,d,e,f;

signed main(){
    ios;
    while(cin>>a>>b>>c>>d>>e>>f){
        if(a==0&&b==0&&c==0&&d==0&&e==0&&f==0)break;
        int ans = f+e+d;
        a = a - 11*e;//5*5可以用11個1*1填
        b = b - 5*d;//一個4*4可以用5個2*2填
        ans+=(c/4);
        if(c%4!=0)ans++;
        if(c%4==1){
            a = a - 7;
            b = b - 5;
        }
        else if(c%4==2){
            a = a - 6;
            b = b - 3;
        }
        else if(c%4==3){
            a = a - 5;
            b = b - 1;
        }
        if(b>0){
            ans+=(b/9);
            if(b%9!=0)ans++;
            a = a-(36-4*(b%9));
        }
        else if(b<0)a = a-(4*(-b));//如果b有欠，可以用a來補
        if(a>0){
            ans+=a/36;
            if(a%36!=0)ans++;
        }
        cout<<ans<<endl;
    }
}
```

### 調校高棕櫚

[題目連結](https://neoj.sprout.tw/problem/91/)
如果想要讓數字盡量小，那勢必要讓數字的位數盡量少
因此可以先把3個2合併成8，2個3合併成9
而剩下的2跟3可以有6 種組合：

| 數字個數 |0個2 | 1個2 |2個2|
| - | - | - |-|
|0個3|無  | 2 |4|
|1個3|2  | 6 |2,6|

先把數字做質因數分解，如果有質數大於10，就輸出-1
接下來，再用這6種組合用**if** 排列組合就完成了

```cpp=
#include <bits/stdc++.h>
#define int long long
using namespace std;
int t;

signed main(){
    cin>>t;
    while(t--){
        int n,arr[11]={0};cin>>n;
        if(n==1){
            cout<<1<<endl;
            continue;
        }
        int ind = 2;
        while(ind<=9){
            if(n%ind==0){
                n /= ind;
                arr[ind]++;
            }
            else ind++;
        }
        if(n>10){
            cout<<-1<<endl;
            continue;
        }
        vector<int>vec;
        
        for(int i=0;i<arr[2]/3;i++)vec.push_back(8);
        arr[2]=arr[2]%3;

        for(int i=0;i<arr[3]/2;i++)vec.push_back(9);
        arr[3]=arr[3]%2;
        
        if(arr[2]==1 && arr[3]==0)vec.push_back(2);
        if(arr[2]==2 && arr[3]==0)vec.push_back(4);
        if(arr[2]==0 && arr[3]==1)vec.push_back(3);
        if(arr[2]==1 && arr[3]==1)vec.push_back(6);
        if(arr[2]==2 && arr[3]==1){
            vec.push_back(2);
            vec.push_back(6);
        }
        for(int i=0;i<arr[5];i++)vec.push_back(5);
        for(int i=0;i<arr[7];i++)vec.push_back(7);
        sort(vec.begin(),vec.end());
        for(auto i:vec)cout<<i;
        cout<<endl;
    }
}
```

## 手寫作業

這一週手寫作業是greedy 正確性的證明，把覺得應該**怎麼**做的想法轉換成數學式子表達，才能讓我們安心的使用greedy ！
蠻開心的，上一次以為分數會很低的手寫作業竟然拿到了100/110，就差一點就可以拿滿分了QQ
