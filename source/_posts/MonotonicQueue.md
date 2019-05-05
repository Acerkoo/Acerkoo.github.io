---
title: 洛谷省选——单调队列
tags: 单调队列
categories: 数据结构    -单调队列
---

## T1

###  题意

[花盆](https://www.luogu.org/problemnew/show/P2698) 

   给出n个点的坐标，以及时间差d。求区间内y坐标的最大值与最小值差大于等于d 的最小x轴区间长度。 **(n<=1e5, d<=1e6, x,y<=1e6)**

 ### 思路
   设l为一段满足条件的区间的左端点，r为该区间最小右端点，则r具有单调性。

   『 单调性证明：
       设端点l1满足条件的最小右端点为r1, 端点l2（l2>=l1)满足条件的最小右端点为r2。
       则： r2>=r1, 否则端点l1满足条件的最小右端点为r2，矛盾。
       故: r具有单调性。                                                   』

   因此可在对n个点按x排序之后，枚举左端点，用单调队列维护满足条件的区间的y轴上的最大值与最小值。

### 代码
```cpp
#include <bits/stdc++.h>
#define I __inline__ __attribute((always_inline))
#define ri register int

using namespace std;
const int inf = 0x3f3f3f3f;
const int maxn = 1e5 + 10;
I char readc(){
    static char buf[100000],*p1=buf,*p2=buf;
    return p1==p2&&(p2=(p1=buf)+fread(buf,1,100000,stdin),p1==p2)?EOF:*p1++;
}
I int readI( ){
    static char c=readc();ri x,f=1;
    for(;c>'9'||c<'0';c=readc()) if(c=='-') f=-1;
    for(x=0;c<='9'&&c>='0';c=readc()) x=(x<<3)+(x<<1)+c-48;
    return x*f;
}

struct node {
    int x, y;
    void read() { 
        x = readI(), y = readI();
    }
    inline bool operator<(const node &p) const {
        return x < p.x;
    }
} rain[maxn];

int q1[maxn], h1=1, t1;
int q2[maxn], h2=1, t2;
int n, d;
int ans = inf;

int main() {
    n = readI(), d = readI();
    for (int i = 1; i <= n; ++i) rain[i].read();
    sort(rain + 1, rain + 1 + n);
    for (register int l = 1, r = 0; l <= n; ++l) {
        while (h1 <= t2 && q1[h1] < l) ++h1;
        while (h2 <= t2 && q2[h2] < l) ++h2;
        while (rain[q1[h1]].y - rain[q2[h2]].y < d && r < n) {
            ++r;
            while (h1 <= t1 && rain[q1[t1]].y < rain[r].y) --t1;
            q1[++t1] = r;
            while (h2 <= t2 && rain[q2[t2]].y > rain[r].y) --t2;
            q2[++t2] = r;
        }
        if (rain[q1[h1]].y - rain[q2[h2]].y >= d)
            ans = min(ans, rain[r].x - rain[l].x);
    }
    if(ans == inf) ans = -1;
    printf("%d\n", ans);
    return 0;
}
```


## T2

### 题意

   [理想的正方形](https://www.luogu.org/problemnew/show/P2216)

   有一个a\*b的整数组成的矩阵，现请你从中找出一个n\*n的正方形区域，使得该区域所有数中的最大值和最小值的差最小。**(a, b<=1000, n<=100)**
### 思路
   首先对于每一行可以用单调队列维护每n个元素的最大值与最小值，并分别存到两个数组中。
   然后对于每一列依旧用单调队列分别维护最大值数组每n个元素的最大值与最小值数组中每n个元素的最小值，得到新的最大值与最小值数组。

   答案即为新的最大值数组与最小值数组差的最小值。

### 代码
```cpp
#include <bits/stdc++.h>
#define ri register int

using namespace std;
const int inf = 0x3f3f3f3f;
const int maxn = 1e3 + 10;

int q1[maxn], q2[maxn];
int mp[maxn][maxn], a, b, n;
int mx[maxn][maxn], mi[maxn][maxn];
int mxy[maxn][maxn], miy[maxn][maxn];
int ans = inf;

int main() {
//    freopen("P2216.in","r", stdin);
    ri h1 = 1, h2 = 1, t1 = 0, t2 = 0;
    scanf("%d%d%d", &a, &b, &n);
    for (ri i = 1; i <= a; ++i) {
        for (ri j = 1; j <= b; ++j)
            scanf("%d", &mp[i][j]);
    }
    for (ri i = 1; i <= a; ++i) {
        h1 = h2 = 1;
        t1 = t2 = 0;
        for (ri j = 1; j <= b; ++j) {
            while (h1 <= t1 && q1[h1] <= j - n) ++h1;
            while (h2 <= t2 && q2[h2] <= j - n) ++h2;
            while (h1 <= t1 && mp[i][q1[t1]] < mp[i][j]) --t1;
            q1[++t1] = j;
            while (h2 <= t2 && mp[i][q2[t2]] > mp[i][j]) --t2;
            q2[++t2] = j;
            if (j >= n) mx[i][j] = mp[i][q1[h1]], mi[i][j] = mp[i][q2[h2]];
        }
    }
    for (ri j = 1; j <= b; ++j) {
        h1 = h2 = 1;
        t1 = t2 = 0;
        for (ri i = 1; i <= a; ++i) {
            while (h1 <= t1 && q1[h1] <= i - n) ++h1;
            while (h2 <= t2 && q2[h2] <= i - n) ++h2;
            while (h1 <= t1 && mx[q1[t1]][j] < mx[i][j]) --t1;
            q1[++t1] = i;
            while (h2 <= t2 && mi[q2[t2]][j] > mi[i][j]) --t2;
            q2[++t2] = i;
            if (i >= n) mxy[i][j] = mx[q1[h1]][j], miy[i][j] = mi[q2[h2]][j];
        }
    }
    for (int i = n; i <= a; ++i) {
        for (int j = n; j <= b; ++j)
            ans = min(ans, mxy[i][j] - miy[i][j]);
    }
    printf("%d\n", ans);
    return 0;
}
```

## T3

### 题意
   [修筑绿化带](https://www.luogu.org/problemnew/show/P2219) 

   在一个n\*m的矩形里，框出一个a\*b的矩形A，在这个矩形内部（不相交）框出一个c\*d的矩形B，使得矩形A的权值和-矩形B的权值和最大，求最大权值和。

   **1<=M,N<=1000,1<=A<=M,1<=B<=N,1<=C<=A-2,1<=D<=B-2，1<=“肥沃度”<=100**

### 思路
   使差最大就 要是被减数尽可能大，减数尽可能小，所以就要用单调队列维护矩形为a\*b的最大值，以及单调队列维护矩形为c\*d的最小值。
   **维护时要注意边界条件**     

### 代码
```cpp
#include <bits/stdc++.h>
using namespace std;
const int maxn = 1e3+10;
int n, m, a, b, c, d;
int mp[maxn][maxn];
int tmp[maxn][maxn], res[maxn][maxn];
int pref[maxn][maxn], preg[maxn][maxn];
int ans = -1;
int que[maxn*maxn], h, t;

int main() {
//    freopen("text.in", "r", stdin);
    scanf("%d%d%d%d%d%d", &n, &m, &a, &b, &c, &d);
    for (int i=1; i<=n; ++i) {
        for (int j=1; j<=m; ++j) {
            scanf("%d", &mp[i][j]);
            mp[i][j] += mp[i-1][j] + mp[i][j-1] - mp[i-1][j-1];
        }
    }

    for (int i=c+1; i<n; ++i) {
        for (int j=d+1; j<m; ++j)
            pref[i][j] = mp[i][j] - mp[i-c][j] - mp[i][j-d] + mp[i-c][j-d];
    }
    for (int i=a; i<=n; ++i) {
        for (int j=b; j<=m; ++j)
            preg[i][j] = mp[i][j] - mp[i-a][j] - mp[i][j-b] + mp[i-a][j-b];
    }

    for (int i=c+1; i<n; ++i) {
        h = 1, t = 0;
        for (int j=d+1; j<m; ++j) {
            while(h<=t && que[h]<=j-(b-d-2)) ++h;
            while(h<=t && pref[i][que[t]]>pref[i][j]) --t;
            que[++t] = j;
            if(j>=b-1) tmp[i][j+1] = pref[i][que[h]];
        }
    }
    for (int j=b; j<=m; ++j) {
        h = 1, t = 0;
        for (int i=c+1; i<n; ++i) {
            while(h<=t && que[h]<=i-(a-c-2)) ++h;
            while(h<=t && tmp[que[t]][j]>tmp[i][j]) --t;
            que[++t] = i;
            if(i>=a-1) res[i+1][j] = tmp[que[h]][j];
        }
    }

    for (int i=a; i<=n; ++i) {
        for (int j=b; j<=m; ++j)
            ans = max(ans, preg[i][j] - res[i][j]);
    }
    printf("%d\n", ans);
    return 0;
}
```

## T4

### 题意

   [生日礼物](https://www.luogu.org/problemnew/show/P2564)

   有K种珠子共n个，分布在x轴上， 求包含k种珠子的最短线段长度。
   **1≤N≤1000000，1≤K≤60，0≤珠子位置<2^31**

### 思路
   将珠子按x坐标排序后，随着x坐标的增大，种类只增不减，因此可以用单调队列维护种类大于等于k时的最短长度。

   『
       由于新加入的珠子除可能影响珠子种类数以外，不会对其他珠子产生影响，所以只需考虑队首元素出队的情况即可。
    』

### 代码
```cpp
#include <bits/stdc++.h>
#define ri register int
using namespace std;
const int maxn = 1e6+10;
struct node {
    int x, kind;
    inline bool operator<(const node &p) const {
        return x<p.x;
    }
}ball[maxn];
int n, K, pn;
int que[maxn];
int cnt[110], res, ans = INT_MAX;

int main() {
    ri h = 1, t = 0;
    scanf("%d%d", &n, &K);
    for (int x, k, i=1; i<=K; ++i) {
        scanf("%d", &k);
        while(k--) {
            scanf("%d", &x);
            ball[++pn] = node{x, i};
        }
    }
    sort(ball+1, ball+1+n);
    for (int i=1; i<=n; ++i) {
        que[++t] = i;
        if(!cnt[ball[que[t]].kind]) ++res;
        ++cnt[ball[que[t]].kind];
        while(res==K) {
            ans = min(ans, ball[que[t]].x - ball[que[h]].x);
            if(h<=t) {
                --cnt[ball[que[h]].kind];
                if(!cnt[ball[que[h]].kind]) --res;
                ++h;
            }
        }
    }
    printf("%d\n", ans);
    return 0;
}
```
    
## T5

### 题意

   [股票交易](https://www.luogu.org/problemnew/show/P2569)

   对于某只股票，已知它未来T天的走势，即 买入价ap, 卖出价bp， 买入数量上限as, 卖出数量上限bs。
   限制条件： 手中最多p股，相邻的两次交易必须间隔w天。
   假设手中有无穷多钱， 0股。
   **0≤W<T≤2000,1≤MaxP≤2000，1≤bp≤ap≤1000,1≤as,bs≤Maxp**

### 思路

   对于第i天有四种情况：

   (1)  从0股开始买， f[i][j] = -ap[i]\*j;

   (2)  啥也不干, f[i][j] = max(f[i][j], f[i-1][j]);

   (3)  买入：f[i][j] = max(f[i][j], f[i-w-1][k]-(j-k)\*ap[i]);

   (4)  卖出：f[i][j] = max(f[i][j], f[i-w-1][k]+(k-j)\*bp[i]);

  对于(3),(4)暴力转移复杂度O(n^3), 仔细分析转移方程性质: max(f[i-w-1][k]+k\*ap[i])-j\*ap[i], 对于转移只需要知道最大的k即可，因此可用单调队列维护以下最大值即可。

  考虑买入与卖出的关系，(3)需要正序,(4)需要倒序。

 ### 代码
 ```cpp
 #include <bits/stdc++.h>
#define ri register int
using namespace std;
const int maxn = 1e6+10;
struct node {
    int x, kind;
    inline bool operator<(const node &p) const {
        return x<p.x;
    }
}ball[maxn];
int n, K, pn;
int que[maxn];
int cnt[110], res, ans = INT_MAX;

int main() {
    ri h = 1, t = 0;
    scanf("%d%d", &n, &K);
    for (int x, k, i=1; i<=K; ++i) {
        scanf("%d", &k);
        while(k--) {
            scanf("%d", &x);
            ball[++pn] = node{x, i};
        }
    }
    sort(ball+1, ball+1+n);
    for (int i=1; i<=n; ++i) {
        que[++t] = i;
        if(!cnt[ball[que[t]].kind]) ++res;
        ++cnt[ball[que[t]].kind];
        while(res==K) {
            ans = min(ans, ball[que[t]].x - ball[que[h]].x);
            if(h<=t) {
                --cnt[ball[que[h]].kind];
                if(!cnt[ball[que[h]].kind]) --res;
                ++h;
            }
        }
    }
    printf("%d\n", ans);
    return 0;
}
 ```