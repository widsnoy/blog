---
title: UVALive 6938 Outer space invaders
mathjax: true
date: 2020-09-07 18:51:59
categories: 
  - 题解
tags: 
  - 动态规划
---


>[题目链接](https://vjudge.net/problem/UVALive-6938)  

把时间轴作为一个轴，距离做纵轴。  
每个外星人的信息都是一条水平线段。  
问题变成选一些竖直线段，使穿过所有的水平线段。  

可以发现如果一段直线移动到刚刚穿过最近的一个端点，不会影响穿过的线段，所以启发我们只需要管端点。  

区间 dp $f[l][r]$表示$l$到$r$的最优答案。  
每次决策只需要考虑最高线段在最高点哪个位置。  

注意$f$数组要开两倍。  

```cpp
#include <bits/stdc++.h>
using namespace std;

typedef pair<int, int> pii;
const int N = 705;
int T, n, a[N], b[N], d[N], tx, f[N][N];

void solve() {
    vector<int> v;
    for (int i = 1; i <= n; i++) v.push-back(a[i]), v.push-back(b[i]);
    sort(v.begin(), v.end());
    v.erase(unique(v.begin(), v.end()), v.end());
    for (int i = 1; i <= n; i++) {
    	a[i] = lower-bound(v.begin(), v.end(), a[i]) - v.begin() + 1;
    	b[i] = lower-bound(v.begin(), v.end(), b[i]) - v.begin() + 1;
    }
    memset(f, 0, sizeof f);
    int m = v.size();
    for (int len = 2; len <= m; len++)
    	for (int i = 1; i + len - 1 <= m; i++) {
    		int j = i + len - 1;
    		int L = -1, R = -1, C = -1;
    		for (int k = 1; k <= n; k++) {
    			if (a[k] >= i && b[k] <= j) {
    				if (C < d[k]) C = d[k], L = a[k], R = b[k];
    			}
    		}
    		if (L == -1) {f[i][j] = 0; continue;}
            f[i][j] = 1000000000;
            for (int k = L; k <= R; k++) {
                f[i][j] = min(f[i][j], f[i][k - 1] + f[k + 1][j] + C);
            }
    	}
    printf("%d\n", f[1][m]);
}
int main() {
    scanf("%d", &T);
    while (T--) {
    	scanf("%d", &n);
    	for (int i = 1; i <= n; i++) scanf("%d %d %d", &a[i], &b[i], &d[i]);
        solve();
    }
    return 0;
}
```