---
title: Dinic 学习笔记
math: true
categories:
  - 模板
tags:
  - 最大流
  - 网络流
abbrlink: '624'
date: 2020-09-16 09:59:00
description:
picUrl:
---


>[最大流模板题](https://www.luogu.com.cn/problem/P3376)  

dinic 是在残量网络上分层后$\mathjaxcal{O}(n^2m)$找增广路的算法  
## 定义
1. 容量：$c(u,v)$表示边最大允许的容量  
2. 流量：$f(u,v)$表示一条有向边中已经占用的流量  
3. 剩余流量：$c(u,v)-f(u,v)$  
4. 残量网络：原图基础上，添加每条边的反向边构成残量网络  
5. 增广路：在残量网络中，一条从原点到汇点的路，所有边的最小流量称为增广流量  
6. 增广：在残量网络中寻找一条增广路，并将所有边流量加上增广流量  
7. 层次：表示节点$u$在层次图中和源点的距离  
8. 层次图：在残量网络中按照每个节点的层次分层，只保留相邻两层节点的图，流量已经等于容量的边不在层次图中  

## 性质
合法的流量图满足三个性质：  
1. 容量限制：$f(u,v)\leq c(u,v)$  
2. 斜对称性：$f(u,v)=-f(v,u)$  
3. 流量守恒：除了源点和汇点，$\sum\limits_{(u,v)\in E}f(u,v)=0$  

## dinic  
最大流问题是最大化$\sum\limits_{(s,v)\in E}f(s,v)$或者$\sum\limits_{(v,t)\in E}f(v,t)$  
dinic 算法在不断 bfs 将图分层后，寻找增广路进行增广。  
当无法增广，即层次图中没有汇点时，增广结束，此时源点到汇点的流量最大。  

反向边是为了反悔，如果当前增广个增广路不是最大流，但已经没有其他增广路。  
可以通过反向边来撤销之前选的流量。  

当前弧优化，在一次找增广路的过程中，如果某条边已经被增广了，那么之后在当前层次图不会用到。  
直接从下一条边开始增广。  

```cpp
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
const int N = 205, M = 1e4 + 5;
const ll inf = 1e16 + 7;
int n, m, s, t, head[N], now[N], cnt = 1;
ll dis[N];
struct E {
    int nxt, v;
    ll w;
} e[M];

void add(int u, int v, ll w) {
	e[++cnt] = {head[u], v, w}; head[u] = cnt;
	e[++cnt] = {head[v], u, 0}; head[v] = cnt;
}

bool bfs() {
    queue<int> q;
	for (int i = 1; i <= n; i++) dis[i] = inf;
	q.push(s); dis[s] = 0;
	now[s] = head[s];
	while (!q.empty()) {
		int u = q.front(); q.pop();
		for (int i = head[u]; i; i = e[i].nxt) {
		    int v = e[i].v;
			if (e[i].w > 0 && dis[v] == inf) {
				now[v] = head[v];
				dis[v] = dis[u] + 1;
				q.push(v);
				if (v == t) return 1;
			}
		}
	}
	return 0;
}
ll dfs(int u, ll sum) {
	if (u == t) return sum;
    ll res = 0, k = 0;
	for (int i = now[u]; i && sum; i = e[i].nxt) {
		now[u] = i;
		int v = e[i].v;
	    if ((dis[v] == dis[u] + 1) && e[i].w > 0) {
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

int main() {
    scanf("%d %d %d %d", &n, &m, &s, &t);
	for (int i = 1; i <= m; i++) {
		int u, v; ll w;
		scanf("%d %d %lld", &u, &v, &w);
	    add(u, v, w);
	}
	ll res = 0;
    while (bfs()) res += dfs(s, inf * 10);
	printf("%lld\n", res);
	return 0;
}

```