---
title: P1344 Pollutant Control
math: true
date: 2020-09-16 10:57:00
categories: 
  - 题解
tags: 
  - 最小割
  - 网络流
  - 图论
description: 
picUrl: 
---


>[题目链接](https://www.luogu.com.cn/problem/P1344)  

最小割模板题  
<!--more-->
根据最小割最大流定理  
$f(s,t)-{max}=c(s,t)-{min}$  
证明如下:  
$f(s,t)\leq c(s,t)$  
$f(s,t)=S\text{出边流量}-S\text{入边流量}\leq S\text{出边流量}=c(s,t)$  
当$s,t$不联通时，入边流量为 0 流，而出边流量满流。  
所以最小割等于最大流  

而割边数只需要把所有边权调为$1$，求一次最大流即可。  

```cpp
#include <bits/stdc++.h>
using namespace std;

const int N = 35, M = 2005, inf = 1e9 + 7;
int n, m, s, t, head[N], cnt = 1, dis[N], now[N];
struct E {
	int nxt, v, w;
} e[M];

void add(int u, int v, int w) {
	e[++cnt] = {head[u], v, w}; head[u] = cnt;
    e[++cnt] = {head[v], u, 0}; head[v] = cnt; 
}

bool bfs() {
	queue<int> q;
	for (int i = 1; i <= n; i++) dis[i] = inf;
	dis[s] = 0; now[s] = head[s];
	q.push(s);
	while (!q.empty()) {
		int u = q.front(); q.pop();
		for (int i = head[u]; i; i = e[i].nxt) {
			int v = e[i].v;
			if (dis[v] == inf && e[i].w > 0) {
				dis[v] = dis[u] + 1;
				now[v] = head[v];
				q.push(v);
				if (v == t) return 1;
			}
		}
	}
	return 0;
}
int dfs(int u, int sum) {
	if (u == t) return sum;
	int k, res = 0;
	for (int i = head[u]; i && sum; i = e[i].nxt) {
		int v = e[i].v;
		if (dis[v] == dis[u] + 1 && e[i].w > 0) {
			k = dfs(v, min(sum, e[i].w));
			if (k == 0) dis[v] = inf;
			e[i].w -= k;
			e[i ^ 1].w += k;
			sum -= k;
			res += k;
		}
	}
	return res;
}
int dinic(int s, int t) {
    int res = 0;
	while (bfs()) res += dfs(s, inf);
	return res;
}

int main() {
    scanf("%d %d", &n, &m);
	s = 1, t = n;
	for (int i = 1; i <= m; i++) {
		int u, v, w;
		scanf("%d %d %d", &u, &v, &w);
		add(u, v, w);
	}
	printf("%d ", dinic(s, t));
	for (int i = 2; i <= cnt; i++) {
        if (i % 2 == 0) e[i].w = 1;
		else e[i].w = 0;
	}
	printf("%d\n", dinic(s, t));
	return 0;
}
```