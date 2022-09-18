---
title: UVA 12170 Easy Climb
math: true
categories:
  - 题解
tags:
  - 动态规划
abbrlink: 2c8
date: 2020-09-07 19:06:00
description:
picUrl:
---


>[题目链接](https://vjudge.net/problem/UVA-12170)  

很容易想到一个状态表示方法$f[i][j]$表示前$i$个点，最后一个高度为$j$时的最小花费。转移只用考虑上一个点填什么。  
但是状态是表示不下的...  

如果只有三个点，因为最左和最右不能改，那么中间的调整范围是$[\max (h-1-b,h-3-b),\min (h-1+b,h-3+b)]$，因为要代价最小，显然只能不调整或者取端点的高度。  

yy 一下，发现其实每个点情况都类似，即$h$最终一定是$p-k+qd$这种形式。  
$1\leq p\leq n$, $-n<q<n$。  

所以把能用到的答案记录下来，第二维就被精简到了$\mathjaxcal{O}(2n^2)$。  

于是现在有了一个$\mathjaxcal{O}(n^5)$的做法。  

按$h$的大小顺序转移，因为每个点的合法区间都是递增的，所以可以用单调队列像滑动窗口那样优化。决策是有单调性的，~~虽然并不太会证~~。  
一个窗口内如果某时刻$f[i-1][j]$增大了，后面的值不会更小，自己画个图看看应该不难理解...  
判断一下不合法的条件是$|h-1-h-n|>(n-1)d$  
时间复杂度$\mathjaxcal{O}(n^3)$

```cpp
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
const int N = 105;
const ll INF = 1e18;
int n, T, tn, q[N], head;
ll a[N * N * 2], d, f[2][N * N * 2], h[N];

void init() {
	tn = 0;
    for (int i = 1; i <= n; i++)
    	for (int j = -n + 1; j <= n - 1; j++) {
    		a[++tn] = h[i] + j * 1ll * d;
    	}
    sort(a + 1, a + tn + 1);
    tn = unique(a + 1, a + tn + 1) - a - 1;
}
void solve() {
	if (abs(h[n] - h[1]) > (n - 1) * 1ll * d) {puts("impossible"); return;}
	init();
	int now = 0;
    for (int i = 1; i <= tn; i++) {
    	f[now][i] = INF;
    	if (a[i] == h[1]) f[now][i] = 0;
    }
    for (int i = 2; i <= n; i++) {
    	now ^= 1;
    	head = 1;
    	for (int j = 1; j <= tn; j++) {
    		while (head < tn && a[head] < a[j] - d) head++;
    		while (head < tn && f[now ^ 1][head + 1] <= f[now ^ 1][head] && a[head + 1] <= a[j] + d) head++;
            if (f[now ^ 1][head] == INF) f[now][j] = INF;
            else f[now][j] = abs(a[j] - h[i]) + f[now ^ 1][head];
    	}
    }
    for (int i = 1; i <= tn; i++) if (a[i] == h[n]) printf("%lld\n", f[now][i]);
}

int main() {
    scanf("%d", &T);
    while (T--) {
    	scanf("%d %lld", &n, &d);
    	for (int i = 1; i <= n; i++) scanf("%lld", &h[i]);
        solve();
    }
}
```