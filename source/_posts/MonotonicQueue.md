---
title: 洛谷省选——单调队列
tags: 单调队列
categories: 数据结构    -单调队列
---

## T1

###  题意

[花盆](https://www.luogu.org/problemnew/show/P2698) 

    给出n个点的坐标，以及时间差d。求区间内y坐标的最大值与最小值差大于等于d 的最小x轴区间长度。 (n<=1e5, d<=1e6, x,y<=1e6) 
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

    有一个a*b的整数组成的矩阵，现请你从中找出一个n*n的正方形区域，使得该区域所有数中的最大值和最小值的差最小。(a, b<=1000, n<=100)
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