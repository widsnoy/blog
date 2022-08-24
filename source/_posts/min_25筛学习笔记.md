---
title: min-25筛学习笔记
mathjax: true
categories:
  - 模板
tags:
  - min-25筛
  - 数论
abbrlink: 14a8
date: 2020-09-20 20:26:00
---

>min-25筛学习笔记
>[参考](https://jkloverdcoi.github.io/2019/06/05/min-25%E7%AD%9B%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/)
>好像有些地方把$F$，$f$搞混了，应该能看懂吧（

$\text{min25}$筛是一种求积性函数前缀和的筛法。
即$\sum\limits_{i=1}^{n}F(i)$，要求$\sum\limits_{i=1}^{n}[i\in prime]*F(i)$可以被快速算出。
时间复杂度 {% raw %} $\mathjaxcal{O}(\frac{n^{{\frac{3}{4}}}}{\log-2n})$ {% endraw %}，空间复杂度$\mathjaxcal{O}(\sqrt{n})$。

## min-25筛
### 预处理

计算$\sum\limits_{i=1}^{cnt}F(p-i)=\sum\limits_{i=1}^{n}[i\in prime]*F(i)$
定义$g(n,j)=\sum\limits_{i=2}^nf(i)*[i\in prime\vee r(i)>p-j]$，$r(i)$表示$i$的最小素约数。
这里，$f$和$F$不一样，$f$的每一个数都和$F$为素数时的计算方法一样，因为好算$g$。
形象理解$r(i)>p-j$ 表示埃氏筛第$j$ 次后还没筛出的数$f(i)$之和。
因为每个$i$都一定有素约数$\leq\sqrt{i}$。
最后$g(n,cnt)$即为所求。

所以${p-j}^2>n$时，$g(n,j)=g(n,j-1)$，因为$p-j$并没有多筛出的数。
${p-j}^2\leq n$时，考虑$p-j$的贡献。
多出来的部分无非就是$r-i=p-j$的数，这些数显然不大于$\lfloor\frac{n}{p-j}\rfloor$，且$r-i>p_{j-1}$。
$g(n,j)=g(n,j-1)-f(p-j)\times (g(\frac{n}{p-j},j-1)-\sum\limits_{k=1}^{j-1}f(p-k))$
因为${p-j}^2\leq n$，所以$\lfloor\frac{n}{p-j}\rfloor\geq p-j$。
因为枚举了$p-1,p-2,\text{...},p_{j-1}$，这些数乘上$p-j$后$r-i<p-j$，所以应当把多算的部分减去。

### 计算
我们现在知道$\sum\limits_{i=1}^{n}F(i)$素数部分的贡献。
设一个二元组$s(n,j)=\sum\limits_{i=2}^nF(i)[r(i)\geq p-j]$

可以发现$\sum\limits_{i=1}^{n}F(i)=s(n,1)+F(1)$
考虑计算$s(n,j)$ ,  运用之前预处理的答案，素数部分的贡献是$g(n,cnt)-\sum\limits_{i=1}^{j-1}f(p-j)$。
合数部分我们枚举最小的质因子$p-k$和幂次$e-i$
{% raw %} $s(n,j)=g(n,cnt)-\sum\limits_{i=1}^{j-1}F(p-j)+\sum\limits_{k=j}^{{p-k}^2\leq n}\sum\limits_{e=1}^{p-k^{e+1}\leq n}F(p-k^e)s(\frac{n}{p-k^e},k+1)+F(p-k^{e+1})$ {% endraw %}
边界是$n=1\vee p-j>n$，$s(n,j)=0$
时间复杂度 {% raw %} $\mathjaxcal{O}(\frac{n^{{\frac{3}{4}}}}{\log-2n})$ {% endraw %}

## 例题
###  区间素数个数
>[题目链接](https://loj.ac/problem/6235)

因为$F(i)=0\text{,}i\notin prime$
所以这道题只需要算$g$，且$f(i)=1$
```cpp
#include <bits/stdc++.h>

typedef long long ll;
const int N = 4e6 + 50;
ll n, id1[N], id2[N], g[N], p[N], w[N], sqr;
int cnt, m;
bool vis[N];

void init(int m) {
    for (int i = 2; i <= m; i++) {
		if (!vis[i]) p[++cnt] = i;
		for (int j = 1; j <= cnt && i * p[j] <= m; j++) {
			vis[i * p[j]] = 1;
			if (i % p[j] == 0) break;
		}
	}
}

int main() {
    scanf("%lld", &n);
	sqr = sqrt(n);
	init(sqr);
	for (ll l = 1, r; l <= n; l = r + 1) {
	    r = n / (n / l);
		w[++m] = n / l;
		g[m] = w[m] - 1;
        if (w[m] <= sqr) id1[w[m]] = m;
		else id2[r] = m;
	}
	for (int j = 1; j <= cnt; j++)
		for (int i = 1; i <= m && p[j] * p[j] <= w[i]; i++) {
			int k = (w[i] / p[j] <= sqr) ? id1[w[i] / p[j]] : id2[n / (w[i] / p[j])];
			g[i] = g[i] - (g[k] - j + 1);
		}
	printf("%lld\n", g[1]);
	return 0;
}
```

### 简单函数
>[题目链接](https://loj.ac/problem/6053)

因为除了$2$的所有约数都是奇数。
可以令所有$f(i)=i-1$，特别的$f(2)$加上$2$即可。
直接套板子就可以了。

```cpp
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
const int N = 2e5 + 5, MOD = 1e9 + 7, inv2 = (MOD + 1) / 2;
ll id1[N], id2[N], n, m, g[N], h[N], P[N], sum[N], w[N];
int cnt, tot;
bool vis[N];

void init() {
	for (ll i = 2; i <= m; i++) {
		if (!vis[i]) P[++cnt] = i, sum[cnt] = (sum[cnt - 1] + i) % MOD;
		for (int j = 1; j <= cnt && P[j] * i <= m; j++) {
			vis[P[j] * i] = 1;
			if (i % P[j] == 0) break;
		}
	}
}

ll fpow(ll a, ll b) {
	ll res = 1;
	for (; b; b >>= 1, a = a * a % MOD) if (b & 1) res = res * a % MOD;
	return res;
}
ll S(ll x, int y) {
	if (x <= 1 || P[y] > x) return 0;
	int k = (x <= m) ? id1[x] : id2[n / x];
	ll res = (g[k] - sum[y - 1] - h[k] + y - 1 + MOD) % MOD;
	if (y == 1) res += 2;
	for (int i = y; i <= cnt && P[i] * P[i] <= x; i++) {
		ll pow1 = P[i], pow2 = P[i] * P[i];
		for (int e = 1; pow2 <= x; e++, pow1 = pow2, pow2 *= P[i]) {
			ll tmp = (S(x / pow1, i + 1) * (P[i] ^ e) % MOD + (P[i] ^ (e + 1))) % MOD;
		    res = (res + tmp) % MOD;
		}
	}
	return res;
}

int main() {
    scanf("%lld", &n);
	m = sqrt(n + 0.5);
	init();
	for (ll l = 1, r; l <= n; l = r + 1) {
		r = n / (n / l);
		w[++tot] = n / l;
		h[tot] = w[tot] % MOD - 1;
		g[tot] = w[tot] % MOD * (w[tot] + 1) % MOD * inv2 % MOD;
		g[tot] = (g[tot] - 1 + MOD) % MOD;
		if (w[tot] > m) id2[r] = tot;
		else id1[w[tot]] = tot;
	}
	for (int j = 1; j <= cnt; j++)
		for (int i = 1; i <= tot && P[j] * P[j] <= w[i]; i++) {
			int k = (w[i] / P[j] <= m) ? id1[w[i] / P[j]] : id2[n / (w[i] / P[j])];
			g[i] = (g[i] - (P[j] * ((g[k] - sum[j - 1] + MOD) % MOD)) % MOD + MOD) % MOD;
		    h[i] = (h[i] - h[k] + j - 1 + MOD) % MOD;
		}
	ll ans = (S(n, 1) + 1) % MOD;
	printf("%lld\n", (ans + MOD) % MOD);
	return 0;
}
```

### Min-25筛
>[题目链接](https://www.luogu.com.cn/problem/P5325)

$F(p)=p(p-1)=p^2-p$
因为要求我们的$f$函数完全积性，所以令$f(i)=i^2$，$f'(i)=i$
定义二元组$g(n,j)=\sum\limits_{i=2}^{n}f(i)[i\in prime\vee r(i) > p[j]]$ ，$h(n,j)=\sum\limits_{i=2}^{n}f'(i)[i\in prime\vee r(i) > p[j]]$

则$s(n,j)=g(n,cnt)-h(n,cnt)-\sum\limits_{i=1}^{j-1}{p-i}^2-p-i$
```cpp
#include <cstdio>
#include <cmathjax>

typedef long long ll;
const int N = 4e6 + 5, MOD = 1e9 + 7;
const ll i6 = 166666668, i2 = 500000004;
ll n, id1[N], id2[N], su1[N], su2[N], p[N], sqr, w[N], g[N], h[N];
int cnt, m;
bool vis[N];

ll add(ll a, ll b) {a %= MOD, b %= MOD; return (a + b >= MOD) ? a + b - MOD : a + b;}
ll mul(ll a, ll b) {a %= MOD, b %= MOD; return a * b % MOD;}
ll dec(ll a, ll b) {a %= MOD, b %= MOD; return ((a - b) % MOD + MOD) % MOD;}

void init(int m) {
	for (ll i = 2; i <= m; i++) {
		if (!vis[i]) p[++cnt] = i, su1[cnt] = add(su1[cnt - 1], i), su2[cnt] = add(su2[cnt - 1], mul(i, i));
		for (int j = 1; j <= cnt && i * p[j] <= m; j++) {
			vis[p[j] * i] = 1;
			if (i % p[j] == 0) break;
		}
	}
}

ll S(ll x, int y) {
	if (p[y] > x || x <= 1) return 0;
	int k = (x <= sqr) ? id1[x] : id2[n / x];
	ll res = dec(dec(g[k], h[k]), dec(su2[y - 1], su1[y - 1]));
	for (int i = y; i <= cnt && p[i] * p[i] <= x; i++) {
		ll pow1 = p[i], pow2 = p[i] * p[i];
		for (int e = 1; pow2 <= x; pow1 = pow2, pow2 *= p[i], e++) {
			ll tmp = mul(mul(pow1, dec(pow1, 1)), S(x / pow1, i + 1));
			tmp = add(tmp, mul(pow2, dec(pow2, 1)));
			res = add(res, tmp);
		}
	}
	return res;
}

int main() {
    scanf("%lld", &n);
	sqr = sqrt(n + 0.5) + 1;
	init(sqr);
	for (ll l = 1, r; l <= n; l = r + 1) {
        r = n / (n / l);
		w[++m] = n / l;
		g[m] = mul(w[m] % MOD, (w[m] + 1) % MOD);
		g[m] = mul(g[m], (2 * w[m] + 1) % MOD);
		g[m] = mul(g[m], i6);
        g[m] = dec(g[m], 1);
		h[m] = mul(w[m] % MOD, (w[m] + 1) % MOD);;
	    h[m] = mul(h[m], i2);
		h[m] = dec(h[m], 1);
	    (w[m] <= sqr) ? id1[w[m]] = m : id2[r] = m;
	}
	for (int j = 1; j <= cnt; j++)
		for (int i = 1; i <= m && p[j] * p[j] <= w[i]; i++) {
			int k = (w[i] / p[j] <= sqr) ? id1[w[i] / p[j]] : id2[n / (w[i] / p[j])];
		    g[i] = dec(g[i], mul(mul(p[j], p[j]), dec(g[k], su2[j - 1])));
			h[i] = dec(h[i], mul(p[j], dec(h[k], su1[j - 1])));
		}
	//printf("%lld\n", g[1] - h[1]);
	printf("%lld\n", add(S(n, 1), 1));
	return 0;
}
```
