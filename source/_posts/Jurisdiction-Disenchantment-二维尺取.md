---
title: Jurisdiction Disenchantment(二维尺取)
date: 2019-04-14 23:36:12
tags: 尺取
categories: 尺取 
---

### 题目描述

The Super League of Paragons and Champions (SLPC) has been monitoring a plot by a corrupt politician to steal an election. In the past week, the politican has used a mind-control technique to enslave the n representatives responsible for choosing the election’s winner. Luckily, the SLPC has managed to recruit you and hence has access to your power to break mind-control. You are able to break mind-control in an axis-aligned rectangle. Unfortunately, your power comes at a steep cost; you get a headache the next day proportional to the size of the rectangle. You do not even want to risk or think about what would happen if you tried to use your power multiple times in one day.
You have done your research and you know the position that each representative will be standing when the votes are about to be cast. You need to free enough representatives to prevent the politician from having a majority (strictly more than one-half) vote. What is the area of the smallest axis-aligned rectangle that you can affect to do this?

### 输入

The first line of input contains a single integer T (1 ≤ T ≤ 10), the number of test cases. The first line of each test case contains a single integer n (1 ≤ n ≤ 299, n is odd), the number of representatives. Each of the next n lines of input contains two integers, denoting the x and y coordinates of a representative. It is guaranteed that all coordinates are between −10,000 and +10,000.

### 输出

For each test case, output a single line containing the area of the smallest axis-aligned rectangle containing more than n/2 of the representatives.

### 样例输入

> 2
> 1
> 1 1
> 3
> 0 0
> 1 4
> 3 2

### 样例输出

> 0
> 4

### 提示

In the first case, a rectangle containing a single point has an area of 0.
In the second test case, the rectangle needs to include at least two points. There are two smallest possible rectangles; one includes (0, 0) and (1, 4) and the other includes (1, 4) and (3, 2). In either case, the area is 4.

### 题意

有 n 个点，需要用一个矩形框 n/2+1 个点，求矩形最小面积。

### 思路

二维尺取，把 n 个点按 x 降序排序， 枚举 x 的边界，维护在这个边界中 n/2+1 个点的最小矩形面积。

### 代码
```cpp
#include <bits/stdc++.h>
using namespace std;
const int maxn = 300;
const int inf = 0x7fffffff;
int T, n;

struct node {
    int x, y;
    void read() {
        scanf("%d%d", &x, &y);
    }
    inline bool operator<(const node &p) {
        return y < p.y;
    }
} a[maxn], b[maxn];

bool cmp(node a, node b) {
    return a.x < b.x || a.x == b.x && a.y < b.y;
}

int main() {
    scanf("%d", &T);
    while (T--) {
        scanf("%d", &n);
        for (int i = 1; i <= n; ++i) {
            a[i].read();
            b[i] = a[i];
        }
        sort(a + 1, a + 1 + n, cmp);
        int ans = INT_MAX, m = n / 2 + 1;
        for (int i = 1; i <= n; ++i) {
            for (int j = i; j <= n; ++j) {
                int lx = a[i].x, rx = a[j].x;
                int cnt = 0;
                for (int k = 1; k <= n; ++k) {
                    if (a[k].x < lx || a[k].x > rx) continue;
                    b[++cnt] = a[k];
                }
                if (cnt >= m) {
                    sort(b + 1, b + 1 + cnt);
                    for (int k = 1; k <= cnt - m + 1; ++k) {
                        int ly = b[k].y, ry = b[k + m - 1].y;
                        ans = min(ans, (rx - lx) * (ry - ly));
                    }
                }
            }
        }
        printf("%d\n", ans);
    }
    return 0;
}
```

