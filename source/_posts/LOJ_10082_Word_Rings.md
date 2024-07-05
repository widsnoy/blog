---
title: LOJ 10082 Word Rings
mathjax: true
categories:
  - 题解
tags:
  - 图论
  - 思维好题
abbrlink: 29bb
date: 2020-09-26 21:34:38
description:
picUrl:
---


>[题目链接](https://loj.ac/problem/10082)  

这道题很妙啊...   

首先这个建边方式，很自然就能想到按题意串与串连边。  
但是这样的复杂度是不能接受的。  

我们只需要将每个串前两个字符对应的节点和后两个对应的节点连边就可以了，不难发现这样也是等价的。  

现在边建好了，很自然就能想到用 spfa 跑最长路。  
这样的时间复杂度也是不能接受的...  

我们并不能直接知道答案是多少，于是二分答案上去验证  
如果一个环$\frac{a-1+a-2+a-3+...+a-n}{n}\geq mid$  
$(a-1-mid)+(a-2-mid)+...+(a-n-mid)\geq 0$  

于是我们可以枚举答案，给每条边一个新的边权，用 dfs 版的 spfa 跑最长路判断有没有正环。  

枚举一定数量的起点来验证，大概率能跑对...   
注意这里不需要每次都 memset 最短路的 dis 数组，因为如果正环上某个点$x$被前驱是$y$，$dis[x]$被重赋值后，看似$dis'[x]\geq dis[y]+w$不能被更新，但是$dis[x]$被重赋值说明已经可以作为新的起点，不需要从$y$迭代过来。  

```cpp
#include <bits/stdc++.h>
using namespace std;

const int N = 50500;
const double eps = 1e-9;
int n, cnt, head[N];
double dis[N];
bool vis[N];
char s[1005];
struct E {int nxt, v; double w;} e[N << 2];
void add(int u, int v, double w) {e[++cnt] = {head[u], v, w}; head[u] = cnt;}

bool dfs(int x, const double mid) {
    vis[x] = 1;
	for (int i = head[x]; i; i = e[i].nxt) {
		int v = e[i].v;
		double w = e[i].w - mid;
		if (dis[v] < dis[x] + w) {
			dis[v] = dis[x] + w;
		    if (vis[v]) return 1;
			if (dfs(v, mid)) return 1;
		}
	}
	vis[x] = 0;
	return 0;
}
bool check(double mid) {
	memset(vis, 0, sizeof vis);
	memset(dis, 0, sizeof dis);
	for (int i = 0; i <= 5000; i++) {
        if (dfs(i, mid)) return 1;
	}
	return 0;
}

int main() {
    while (scanf("%d", &n) != EOF && n) {
		memset(head, 0, sizeof head);
		cnt = 0;
		for (int i = 1; i <= n; i++) {
			scanf("%s", s + 1);
			int len = strlen(s + 1);
		    if (len < 2) continue;
            int pre = (s[1] - 'a') * 26, suf = (s[len - 1] - 'a') * 26;
			pre = pre + s[2] - 'a', suf = suf + s[len] - 'a';
			double bl = 1.0 * len;
			add(pre, suf, bl);
		}
		double l = 0.0, r = 1005.0;
		while (r - l > eps) {
			double mid = (r + l) / 2.0;
			if (check(mid)) l = mid;
			else r = mid;
		}
		if (l == 0.0) puts("No solution");
		else printf("%.2lf\n", l);
	}
	return 0;
}
```