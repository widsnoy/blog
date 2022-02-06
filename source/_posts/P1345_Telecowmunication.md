---
title: P1345 Telecowmunication
math: true
date: 2020-09-16 12:29:08
categories: 
  - 题解
tags: 
  - 网络流
  - 最小割
  - 图论
description: 
picUrl: 
---


>[题目链接](https://www.luogu.com.cn/problem/P1345)  

这道题是删点  
那我们想办法把它变成删边的题，就是删除某条边和删一个点效果一样  
<!--more-->
把每个点$i$拆成$i+n$和$i$，$i$连入边，$i+n$连所有出边。  
再把$i$和$i+n$连上一条边。  
那么删除$i$和$i+n$间的边和删除$i$点效果一样  
就变成了求最小割的题  

```cpp
#include <bits/stdc++.h>
using namespace std;

const int N = 205, M = 2605, inf = 1 << 25;
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
    for (int i = 1; i <= 2 * n; i++) dis[i] = inf;
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
    for (int i = now[u]; i && sum; i = e[i].nxt) {
        now[u] = i;
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
    while (bfs())  res += dfs(s, inf);
    return res;
}

int main() {
    scanf("%d %d %d %d", &n, &m, &s, &t);
	for (int i = 1; i <= n; i++) add(i, i + n, 1);
    for (int i = 1; i <= m; i++) {
		int u, v;
		scanf("%d %d", &u, &v);
		add(u + n, v, inf);
		add(v + n, u, inf);
	}
	s += n;
	printf("%d\n", dinic(s, t));
	return 0;
}

```