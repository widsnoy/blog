---
title: P2057 Vote 善意的投票
math: true
date: 2020-09-16 15:39:06
categories: 
  - 题解
tags: 
  - 网络流
  - 最小割
description: 
picUrl: 
---


>[题目链接](https://www.luogu.com.cn/problem/P2057)  

最小割经典模型  
<!--more-->
把投票的$0$，$1$分别看作源点和汇点。  
每个小朋友都和自己投票的种类连一条边。  
而好朋友直接也连一条边。  

如果好朋友不在一个集合，他们的边会断，反之不会。  
而自己不在想投的集合，边会断，反之不会。  

要求断边最小，把所有边权设为$1$跑一次最小割就可以了。  

```cpp
#include <bits/stdc++.h>
using namespace std;

const int N = 305, M = N * N * 2, inf = 1 << 30;
int n, m, s, t, head[N], cnt = 1, dis[N], now[N];
struct E {
	int nxt, v, w;
} e[M];

void add(int u, int v, int w) {
	e[++cnt] = {head[u], v, w}; head[u] = cnt;
	e[++cnt] = {head[v], u, 0}; head[v] = cnt;
}

bool bfs() {
    for (int i = 1; i <= n + 2; i++) dis[i] = inf;
	dis[s] = 0;
	now[s] = head[s];
	queue<int> q;
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
	for (int i = now[u]; i && sum; i = e[i].nxt) {
		int v = e[i].v;
		now[u] = i;
		if (dis[v] == dis[u] + 1 && e[i].w > 0) {
			k = dfs(v, min(sum, e[i].w));
			if (k == 0) dis[v] = inf;
			e[i].w -= k;
			e[i ^ 1].w += k;
			res += k;
			sum -= k;
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
	s = n + 1, t = n + 2;
	for (int i = 1; i <= n; i++) {
		int x; scanf("%d", &x);
        if (x == 0) add(s, i, 1);
		else add(i ,t, 1);
	}
	for (int i = 1; i <= m; i++) {
		int u, v;
		scanf("%d %d", &u, &v);
		add(u, v, 1);
		add(v, u, 1);
	}
	printf("%d\n", dinic(s, t));
	return 0;
}

```