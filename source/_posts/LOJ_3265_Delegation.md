---
title: LOJ 3265 Delegation
math: true
date: 2020-09-23 07:43:39
categories: 
  - 题解
tags: 
  - 贪心
description: 
picUrl: 
---


>[题目链接](https://loj.ac/problem/3265)  

这题看起来挺像赛道修建。  
<!--more-->
但是是要求把所有路选完，所以贪心方法不太一样。  

首先，二分以后并不是能选就选，这个看样例就知道。  
```
8
1 2
1 3
1 4
4 5
1 6
6 7
7 8
```
$\text{mid}=2$时，如果能选就选最后是不合法的，因为有边最后不能放到长度不小于$2$的链里面。  

考虑最后合并一个点的所有链，如果有多于一条链最后不能合并，那就是不合法的。  
所有链都满足要求的前提下，可以选取一条尽量长的链给父节点，这样一定是不劣的，因为可以顺便把这条链合并了，也有可能这条链刚好时父节点合并完。  

最后特别判断一下父节点。  
```cpp
#include <bits/stdc++.h>
using namespace std;

const int N = 1e5 + 4;
int n, mid, head[N], cnt;
struct E {
	int nxt, v;
} e[N << 1];

void add(int u, int v) {
	e[++cnt] = {head[u], v}; head[u] = cnt;
	e[++cnt] = {head[v], u}; head[v] = cnt;
}

int dfs(int u, int fa) {
	multiset<int> s;
    for (int i = head[u]; i; i = e[i].nxt) {
		int v = e[i].v;
		if (v == fa) continue;
		int d = dfs(v, u);
		if (d == -1) return -1;
		s.insert(d + 1);
	}
	int t = 0, pt = 0;
	while (!s.empty()) {
		auto it = s.begin();
		int x = *it;
		s.erase(it);
		auto itl = s.lower-bound(mid - x);
		if (itl == s.end()) {
			if (!t) t = x;
			else return -1;
		} else {
			if (*itl >= mid) pt = x;
			s.erase(itl);
		}
	}
	if (u != 1) return t ? t : pt;
	return t;
}

int main() {
    scanf("%d", &n);
	for (int i = 1; i < n; i++) {
		int u, v;
		scanf("%d %d", &u, &v);
		add(u, v);
	}
	int l = 1, r = n - 1, ans = 0;
    while (l <= r) {
		mid = (l + r) >> 1;
		int x = dfs(1, 0);
		if (x == 0 || x >= mid) l = mid + 1, ans = mid;
		else r = mid - 1;
	}
	printf("%d\n", ans);
	return 0;
}
```
