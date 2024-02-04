---
title: LOJ 3282 「JOISC 2020 Day4」治疗计划
mathjax: true
categories:
  - 题解
abbrlink: adda
date: 2020-08-31 18:36:00
tags:
---


>[题目链接](https://loj.ac/problem/3282)  

两个计划的治疗区间$[l-i,r-i]$, $[l-j,r-j]$可以合并，当且仅当$[r-i-l-j+1\geq \left |T-i-T-j\right |]$  
按照区间顺序，可以有$\mathjaxcal{O}(m^2)$的暴力 dp , 每次选出一个最小的去合并其他区间。  
```cpp
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
const int N = 1e5 + 5;
const ll INF = 1e18;
int n, m, t[N], r[N], l[N];
ll f[N], c[N];
bool vis[N];

int find() {
	ll ans = INF; int pos = 0;
	for (int i = 1; i <= m; i++) {
		if (!vis[i] && f[i] < ans) {
			pos = i;
			ans = f[i];
		}
	}
	return pos;
}

int main() {
    scanf("%d %d", &n, &m);
    for (int i = 1; i <= m; i++) {
    	scanf("%d %d %d %lld", &t[i], &l[i], &r[i], &c[i]);
    	if (l[i] == 1) f[i] = c[i];
        else f[i] = INF;
    }
    for (int pos = find(); pos; pos = find()) {
        vis[pos] = 1;
        for (int i = 1; i <= m; i++) {
            if (r[pos] - l[i] + 1 >= abs(t[i] - t[pos])) f[i] = min(f[i], f[pos] + (ll)c[i]);
        }
    }
    ll ans = INF;
    for (int i = 1; i <= m; i++) if (r[i] == n) ans = min(ans, f[i]);
    printf("%lld\n", ans == INF ? -1 : ans);
    return 0;
}
```

可以看出这个 dp 可以用最短路来做。  
把之前的式子绝对值去掉，  
$T-i\geq T-j$时,$r-i-T-i+1\geq l-j-T-j$  
$T-i<T-j$时,$r-i+T-i+1\geq l-j+T-j$    
按照时间顺序维护两颗线段树，每次用线段树找出边。  
最短路的时候更新 dis 就可以了。
```cpp
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
const int N = 1e5 + 5, INF = 2e9 + 1;
struct data {
	int t, l, r;ll c;
	bool operator < (const data &x) const {
		return t < x.t;
	} 
} qaq[N];
vector<int> v;
int n, m;
ll dis[N];

struct Segment-Tree {
	#define lson (p << 1)
	#define rson (p << 1 | 1)
	#define mid ((l + r) >> 1)
    int mi[N << 2];
    void build(int p, int l, int r, int sig) {
    	if (l == r) {
    		mi[p] = qaq[l].l + sig * qaq[l].t;
    		return;
    	}
    	build(lson, l, mid, sig);
    	build(rson, mid + 1, r, sig);
    	mi[p] = min(mi[lson], mi[rson]);
    }
    void erase(int p, int l, int r, int pos) {
        if (l == r) {
        	mi[p] = INF;
        	return;
        }
        if (pos <= mid) erase(lson, l, mid, pos);
        else erase(rson, mid + 1, r, pos);
        mi[p] = min(mi[lson], mi[rson]);
    }
    void qry(int p, int l, int r, int L, int R, int bound) {
        if (mi[p] > bound) return;
        if (l == r) {
        	v.push-back(l);
        	return;
        }
        if (L <= mid)qry(lson, l, mid, L, R, bound);
        if (R > mid) qry(rson, mid + 1, r, L, R, bound);
    }
} T1, T2;

priority-queue<pair<ll, int> > q;
ll dij() {
	for (int i = 1; i <= m; i++) {
		if (qaq[i].l == 1) {
			q.push({-qaq[i].c, i});
            dis[i] = qaq[i].c;
            T1.erase(1, 1, m, i);
            T2.erase(1, 1, m, i);
		}
	}
	while (!q.empty()) {
		int u = q.top().second;
		q.pop();
		if (qaq[u].r == n) return dis[u];
		v.clear();
		if (u > 1) T1.qry(1, 1, m, 1, u - 1, qaq[u].r - qaq[u].t + 1);
		if (u < m) T2.qry(1, 1, m, u + 1, m, qaq[u].r + qaq[u].t + 1);
		for (auto i : v) {
			dis[i] = dis[u] + qaq[i].c;
			q.push({-dis[i], i});
			T1.erase(1, 1, m, i);
			T2.erase(1, 1, m, i);
		}
	}
	return -1;
}

int main() {
    scanf("%d %d", &n, &m);
    for (int i = 1; i <= m; i++) {
    	scanf("%d %d %d %lld", &qaq[i].t, &qaq[i].l, &qaq[i].r, &qaq[i].c);
    }
    sort(qaq + 1, qaq + m + 1);
    T1.build(1, 1, m, -1);
    T2.build(1, 1, m, 1);
    printf("%lld\n", dij());
    return 0;
}
```