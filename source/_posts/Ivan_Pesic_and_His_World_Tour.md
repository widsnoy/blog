---
title: Ivan Pesic and His World Tour
math: true
categories:
  - 题解
tags:
  - hash
  - 树链剖分
abbrlink: 4a7
date: 2020-10-14 18:56:14
---


>[题目链接](https://www.codechef.com/LTIME73A/problems/QRYLAND)

## 题目
给定一颗  $n$  个点的树。每个点都一个正整数点权  $Ai$  ，你需要支持以下两种操作：
1、询问点  $x$  和点  $y$  之间的路径上的所有点（包括点  $x$  和点  $y$ ）的点权是否构成一个从  $1$  开始的排列（即若这条链长度为  $len$  ，那么问点权集合是否为  ${1,2,⋯,len}$  ）。

2、将  $Ax$  修改为  $y$  。

## 题解
这道题就是把[BZOJ4373](https://darkbzoj.tk/problem/4373)上树的版本。  
判一个排列直接用自然数幂之和或者是异或和 + hash

有个坑点，当然是对我来说。。。 
我用的自然溢出，所以不能随便除数的，所以不应该这么算自然数幂之和
```cpp
ull su1(int x) {return (x + 1ull) * 1ull * x / 2ull;}
ull su2(int x) {return x * 1ull * (ull)(x + 1) * 1ull * (ull)(x + x + 1) / 6;}
ull su3(int x) {return x * 1ull * (ull)(x + 1) * 1ull * x * 1ull * (ull)(x + 1) / 4ull;}
```
自然溢出相当于$\%2^{64}$，显然溢出了不能随便除  

直接预处理自然数幂之和不就完了。。。。  
```cpp
for (ull i = 1; i < N; i++) su1[i] = su1[i - 1] + i, su2[i] = su2[i - 1] + i * i, su3[i] = su3[i - 1] + i * i * i;
```  

## 代码
```cpp
#include <cstdio>
#include <algorithm>
#include <cstring>
using namespace std;

typedef long long ll;
typedef unsigned long long ull;
const int N = 2e5 + 5e4 + 5;
int n, q, head[N], cnt, a[N];
struct E {
	int nxt, v;
} e[N << 1];

void add(int u, int v) {
	e[++cnt] = {head[u], v}; head[u] = cnt;
	e[++cnt] = {head[v], u}; head[v] = cnt;
}

ull sum2[N << 2], sum3[N << 2], su1[N], su2[N], su3[N];
ll sum[N << 2];
int dep[N], rnk[N], dfn[N], fa[N], top[N], son[N], dfs-clock, siz[N];

#define mid ((l + r) >> 1)
#define lson (p << 1)
#define rson ((p << 1) | 1)

void build(int p, int l, int r) {
	if (l == r) {
		sum[p] = a[rnk[l]];
		sum2[p] = a[rnk[l]] * 1ull * a[rnk[l]];
		sum3[p] = a[rnk[l]] * 1ull * a[rnk[l]] * 1ull * a[rnk[l]];
		return;
	}
	build(lson, l, mid);
	build(rson, mid + 1, r);
	sum[p] = sum[lson] + sum[rson];
	sum2[p] = sum2[lson] + sum2[rson];
	sum3[p] = sum3[lson] + sum3[rson];
}
void modify(int p, int l, int r, int pos, int v) {
	if (l == r) {
		sum[p] = v;
		sum2[p] = v * 1ull * v;
		sum3[p] = v * 1ull * v * 1ull * v;
		return;
	}
	if (pos <= mid) modify(lson, l, mid, pos, v);
	else modify(rson, mid + 1, r, pos, v);
	sum[p] = sum[lson] + sum[rson];
	sum2[p] = sum2[lson] + sum2[rson];
	sum3[p] = sum3[lson] + sum3[rson];
}
ll qry-sum(int p, int l, int r, int L, int R) {
	if (l > R || r < L) return 0;
	if (l >= L && r <= R) return sum[p];
	return qry-sum(lson, l, mid, L, R) + qry-sum(rson, mid + 1, r, L, R);
}
ull qry-sum2(int p, int l, int r, int L, int R) {
	if (l > R || r < L) return 0;
	if (l >= L && r <= R) return sum2[p];
	return qry-sum2(lson, l, mid, L, R) + qry-sum2(rson, mid + 1, r, L, R);
}
ull qry-sum3(int p, int l, int r, int L, int R) {
	if (l > R || r < L) return 0;
	if (l >= L && r <= R) return sum3[p];
	return qry-sum3(lson, l, mid, L, R) + qry-sum3(rson, mid + 1, r, L, R);
}

void dfs1(int u, int f) {
    fa[u] = f; siz[u] = 1; son[u] = -1; dep[u] = dep[f] + 1;
    for (int i = head[u]; i; i = e[i].nxt) {
        int v = e[i].v;
        if (v == f) continue;
        dfs1(v, u);
        siz[u] += siz[v];
        if (son[u] == -1 || siz[v] > siz[son[u]]) son[u] = v;
    }
}
void dfs2(int u, int t) {
    top[u] = t; dfn[u] = ++dfs-clock; rnk[dfs-clock] = u;
    if (son[u] == -1) return;
    dfs2(son[u], t);
    for (int i = head[u]; i; i = e[i].nxt) {
        int v = e[i].v;
        if (v == fa[u] || v == son[u]) continue;
        dfs2(v, v);
    }
}
bool qry(int u, int v) {
	int len = dep[u] + dep[v];
    ull res1 = 0, res2 = 0, res3 = 0;
	while (top[u] != top[v]) {
		if (dep[top[u]] < dep[top[v]]) swap(u, v);
		res1 = res1 + qry-sum(1, 1, n, dfn[top[u]], dfn[u]);
		res2 = res2 + qry-sum2(1, 1, n, dfn[top[u]], dfn[u]);
		res3 = res3 + qry-sum3(1, 1, n, dfn[top[u]], dfn[u]);
		u = fa[top[u]];
	}
	if (dep[u] < dep[v]) swap(u, v);
	res1 = res1 + qry-sum(1, 1, n, dfn[v], dfn[u]);
	res2 = res2 + qry-sum2(1, 1, n, dfn[v], dfn[u]);
	res3 = res3 + qry-sum3(1, 1, n, dfn[v], dfn[u]);
	len -= 2 * dep[v] - 1;
	return ((res1 == su1[len]) && (res2 == su2[len]) && (res3 == su3[len]));
}

int main() {
	int -;
	for (ull i = 1; i < N; i++) su1[i] = su1[i - 1] + i, su2[i] = su2[i - 1] + i * i, su3[i] = su3[i - 1] + i * i * i;
    for (scanf("%d", &-); -; ---) {
    	cnt = 0; dfs-clock = 0;
    	memset(head, 0, sizeof head);
    	scanf("%d %d", &n, &q);
        for (int i = 1; i <= n; i++) scanf("%d", &a[i]);
        for (int i = 1; i < n; i++) {
        	int u, v;
        	scanf("%d %d", &u, &v);
        	add(u, v);
        }
        dfs1(1, 0);
        dfs2(1, 1);
        build(1, 1, n);
        while (q--) {
        	int op, x, y;
        	scanf("%d %d %d", &op, &x, &y);
            if (op == 1) qry(x, y) ? puts("Yes") : puts("No");
            else if (op == 2) modify(1, 1, n, dfn[x], y);
        }
    }
    return 0;
}
```