---
title: polynomial 学习笔记
categories: ACM
tags:
  - 多项式
mathjax: true
abbrlink: '7026'
date: 2024-07-20 14:59:53
updated:
keywords:
description:
top_img:
comments:
cover:
toc:
toc_number:
toc_style_simple:
copyright:
copyright_author:
copyright_author_href:
copyright_url:
copyright_info:
aplayer:
highlight_shrink:
aside:
---

## 生成函数
- 常用封闭形式
1.  $a=\langle 0,1,1,1,1,\cdots\rangle \rightarrow \dfrac{x}{1-x}$  

2.  $a=\langle 1,0,1,0,1,\cdots \rangle$   
    $$
    \begin{aligned}
    F(x)&=\sum_{n\ge 0}x^{2n}\\
    &=\sum_{n\ge 0}(x^2)^{n}\\
    &=\frac{1}{1-x^2}
    \end{aligned}
    $$

3.  $a=\langle 1,2,3,4,\cdots \rangle$ 
$$\begin{aligned}F(x)&=\sum_{n\ge 0}(n+1)x^n\\&=\sum_{n\ge 1}nx^{n-1}\\&=\sum_{n\ge 0}(x^n)'\\&=\left(\frac{1}{1-x}\right)'\\&=\frac{1}{(1-x)^2}\end{aligned}$$
4.  $a_n=\binom{m}{n}$（$m$ 是常数，$n\ge 0$）
$$ F(x)=\sum_{n\ge 0}\binom{m}{n}x^n=(1+x)^m$$  

5.  $a_n=\binom{m+n}{n}$（$m$ 是常数，$n\ge 0$）
$$F(x)=\sum_{n\ge 0}\binom{m+n}{n}x^n=\frac{1}{(1-x)^{m+1}}$$

- 牛顿二项式定理

我们重新定义组合数的运算：

$$
\binom{r}{k}=\frac{r^{\underline{k}}}{k!}\quad(r\in\mathbf{C},k\in\mathbf{N})
$$

注意 $r$ 的范围是复数域。在这种情况下。对于 $\alpha\in\mathbf{C}$，有

$$
(1+x)^{\alpha}=\sum_{n\ge 0}\binom{\alpha}{n}x^n
$$

二项式定理其实是牛顿二项式定理的一个特殊情况。

- 例题: [BZOJ 3028 食物](https://new.bzoj.org:88/p/P3028)

先写出 $8$ 种食物各自方案数的生成函数
1. $\dfrac{1}{1-x^2}$  
2. $1+x$
3. $1+x+x^2$
4. $\dfrac{x}{1-x^2}$
5. $\dfrac{1}{1-x^4}$
6. $1+x+x^2+x^3$
7. $1+x$
8. $\dfrac{1}{1-x^3}$

总的方案的生成函数是各自生产函数的卷积，封闭形式是 $F(x)=\dfrac{x}{(1-x)^4}$  
设 $G(x)=\sum\limits_{n\ge 0}\binom{n+3}{n}x^n=\dfrac{1}{(1-x)^4}$  
$
F(x)=xG(x)=\sum_{n\ge 1}\binom{n+2}{n-1}x^n
$

## 多项式科技
### 分治 FFT
“分治 + FFT”  

形如 $\prod 1+x^i$ 的柿子，直接分治算复杂度为 $O(NlogN)$

### 半在线卷积

给定序列 $g_{1\dots n - 1}$，求序列 $f_{0\dots n - 1}$。

其中 $f_i=\sum_{j=1}^if_{i-j}g_j$，边界为 $f_0=1$。

CDQ 分治考虑 $[l,mid]$ 对 $(mid,r]$ 贡献。  

$
\begin{aligned}
w_x&=\sum\limits_{i=l}^{mid}f_i g_{x-i}\\
&=\sum\limits_{i=0}^{mid-l}f_{i+l}g_{x-i-l}\\
&=\sum\limits_{i=0}^{x-l-1}f_{i+l}g_{x-i-l}\\
\end{aligned}
$

第三步是因为 $f_{mid+1\dots r}=0$。令 $a_i=f_{i+l}$, $b_i=g_{i+1}$, 原式等于 $w_x=\sum\limits_{i=0}^{x-l-1}a_i b_{x-i-l-1}$
### 多形式求逆  

给定一个多项式 $F(x)$ ，请求出一个多项式 $G(x)$， 满足 $F(x) * G(x) \equiv 1 \pmod{x^n}$。  

显然有 $G(0)=\dfrac{1}{F(0)}$，$\forall n\ge 1, \ \sum\limits_{i=0}^nF(i)G(n-i)=0 \Rightarrow F(n)=\dfrac{-1}{G(0)}\sum\limits_{i=0}^{n-1}F(i)G(n-i)$  于是可以 $O(n^2)$ 递推。  

- 倍增法

$$
\left[x^{0}\right]f^{-1}\left(x\right)=\left(\left[x^{0}\right]f\left(x\right)\right)^{-1}
$$

假设现在已经求出了 $f\left(x\right)$ 在模 $x^{\left\lceil\frac{n}{2}\right\rceil}$ 意义下的逆元 $f^{-1}_{0}\left(x\right)$。
有：

$$
\begin{aligned}
	f\left(x\right)f^{-1}_{0}\left(x\right)&\equiv 1 &\pmod{x^{\left\lceil\frac{n}{2}\right\rceil}}\\
	f\left(x\right)f^{-1}\left(x\right)&\equiv 1 &\pmod{x^{\left\lceil\frac{n}{2}\right\rceil}}\\
	f^{-1}\left(x\right)-f^{-1}_{0}\left(x\right)&\equiv 0 &\pmod{x^{\left\lceil\frac{n}{2}\right\rceil}}
\end{aligned}
$$

两边平方可得：

$$
f^{-2}\left(x\right)-2f^{-1}\left(x\right)f^{-1}_{0}\left(x\right)+f^{-2}_{0}\left(x\right)\equiv 0 \pmod{x^{n}}
$$

两边同乘 $f\left(x\right)$ 并移项可得：

$$
f^{-1}\left(x\right)\equiv f^{-1}_{0}\left(x\right)\left(2-f\left(x\right)f^{-1}_{0}\left(x\right)\right) \pmod{x^{n}}
$$

### 多项式 EXP
使用半在线卷积解决
$$
\begin{aligned}
f(x)&=\exp(g(x))\\
f'(x)&=g'(x)\exp(g(x))\\
f'(x)&=g'(x)f(x)\\
f(x)&=\int g'(x)f(x)\\
[x^n]f(x)&=\frac{1}{n}[x^{n-1}]g'(x)f(x)\\
f_n&=\frac{1}{n}\sum\limits_{k=0}^{n-1}f_{k}((n-k)g_{n-k})\\
f_n&=\frac{1}{n}\sum\limits_{k=0}^{n-1}f_{k}b_{n-k}\\
f_n&=\frac{1}{n}\sum\limits_{k=1}^nf_{n-k}b_k
\end{aligned}
$$

- 整数拆分  

$P(n)$ 为 $n$ 拆分为若干个正整数的方案数，$P(n,k)$ 为 $n$ 拆分为 $k$ 个正整数的方案数。  
$P(n)=\sum\limits_{i=1}^nP(n,i)$  
$P(n,k)=P(n-1,k-1)+P(n-k,k)$  
$Z(n,k)$ 为 $n$ 拆分为若干个自然数的方案数，$Z(n-k,k)=P(n,k)\Rightarrow Z(n,k)=Z(n-k,k)+Z(n-1,k-1)$  
生成函数 $F_k(x)=\sum\limits_{i\ge0}Z(i,k)x^i$，则有 $F_k(x)=F_{k-1}(x)+x^kF_k(x)$  
$F_k=\prod\limits_{i\ge1}^k\dfrac{1}{1-x^i}$，分母可以用分治 + FFT或者取对数算出，然后再求逆。  

这个封闭形式，也可以根据 Ferries 图的理论，将 $n$ 拆分成 $k$ 个自然数的方案数等于将 $n$ 拆分为若干个不大于 $k$ 的自然数的方案数，这是一个完全背包，可以用生成函数优化。  
填 $1$ 的生成函数: $\sum x^i=\dfrac{1}{1-x}$, 填 $2$ 的生成函数: $\sum x^{2i}=\dfrac{1}{1-x^2}$, 填 $3$ 的生成函数: $\sum x^{3i}=\dfrac{1}{1-x^3}\dots$  

## code
```cpp
#include <bits/stdc++.h>
using namespace std;

typedef vector<int> poly;
const int mod = 998244353;
const int N = 4000000 + 5;

int rf[26][N];
int fpow(int a, int b) {
    int res = 1;
    for (; b; b >>= 1, a = a * 1ll * a % mod) if (b & 1)
        res = res * 1ll * a % mod;
    return res;
}
void init(int n) {
    assert(n < N);
    int lg = __lg(n);
    static vector<bool> bt(26, 0);
    if (bt[lg] == 1) return;
    bt[lg] = 1;
    for (int i = 0; i < n; i++) rf[lg][i] = (rf[lg][i >> 1] >> 1) + ((i & 1) ? (n >> 1) : 0);
}
void ntt(poly &x, int lim, int op) {
    int lg = __lg(lim), gn, g, tmp;
    for (int i = 0; i < lim; i++) if (i < rf[lg][i]) swap(x[i], x[rf[lg][i]]);
    for (int len = 2; len <= lim; len <<= 1) {
        int k = (len >> 1);
        gn = fpow(3, (mod - 1) / len);
        for (int i = 0; i < lim; i += len) {
            g = 1;
            for (int j = 0; j < k; j++, g = gn * 1ll * g % mod) {
                tmp = x[i + j + k] * 1ll * g % mod;
                x[i + j + k] = (x[i + j] - tmp + mod) % mod;
                x[i + j] = (x[i + j] + tmp) % mod;
            }
        }
    }
    if (op == -1) {
        reverse(x.begin() + 1, x.begin() + lim);
        int inv = fpow(lim, mod - 2);
        for (int i = 0; i < lim; i++) x[i] = x[i] * 1ll * inv % mod;
    }
}
poly multiply(const poly &a, const poly &b) {
    assert(!a.empty() && !b.empty());
    int lim = 1;
    while (lim + 1 < int(a.size() + b.size())) lim <<= 1;
    init(lim);
    poly pa = a, pb = b;
    pa.resize(lim);
    pb.resize(lim);
    ntt(pa, lim, 1); ntt(pb, lim, 1);
    for (int i = 0; i < lim; i++) pa[i] = pa[i] * 1ll * pb[i] % mod;
    ntt(pa, lim, -1);
    pa.resize(int(a.size() + b.size()) - 1);
    return pa;
}
poly prod_poly(const vector<poly>& vec) {
    int n = vec.size();
    auto calc = [&](const auto &self, int l, int r) -> poly {
        if (l == r) return vec[l];
        int mid = (l + r) >> 1;
        return multiply(self(self, l, mid), self(self, mid + 1, r));
    };
    return calc(calc, 0, n - 1);
}

// Semi-Online-Convolution
poly semi_online_convolution(const poly& g, int n, int op = 0) {
    assert(n == g.size());
    poly f(n, 0);
    f[0] = 1;
    auto CDQ = [&](const auto &self, int l, int r) -> void {
        if (l == r) {
            // exp
            if (op == 1 && l > 0) f[l] = f[l] * 1ll * fpow(l, mod - 2) % mod;
            return;
        }
        int mid = (l + r) >> 1;
        self(self, l, mid);
        poly a, b;
        for (int i = l; i <= mid; i++) a.push_back(f[i]);
        for (int i = 0; i <= r - l - 1; i++) b.push_back(g[i + 1]);
        a = multiply(a, b);
        for (int i = mid + 1; i <= r; i++) f[i] = (f[i] + a[i - l - 1]) % mod;
        self(self, mid + 1, r);
    };
    CDQ(CDQ, 0, n - 1);
    return f;
}

poly getinv(const poly &a) {
    assert(!a.empty());
    poly res = {fpow(a[0], mod - 2)}, na = {a[0]};
    int lim = 1;
    while (lim < int(a.size())) lim <<= 1;
    for (int len = 2; len <= lim; len <<= 1) {
        while (na.size() < len) {
            int tmp = na.size();
            if (tmp < a.size()) na.push_back(a[tmp]);
            else na.push_back(0);
        }
        auto tmp = multiply(na, res);
        for (auto &x : tmp) x = (x > 0 ? mod - x : x);
        tmp[0] = ((tmp[0] + 2) >= mod) && (tmp[0] -= mod);
        tmp = multiply(res, tmp);
        tmp.resize(len);
        res = tmp;
    }
    res.resize(a.size());
    return res;
}
poly exp(const poly &g) {
    int n = g.size();
    poly b(n, 0);
    for (int i = 1; i < n; i++) b[i] = i * 1ll * g[i] % mod;
    return semi_online_convolution(b, n, 1);
}
poly ln(const poly &A) {
    int n = A.size();
    auto C = getinv(A);
    poly A1(n, 0);
    for (int i = 0; i < n - 1; i++) A1[i] = (i + 1) * 1ll * A[i + 1] % mod;
    C = multiply(C, A1);
    for (int i = n - 1; i > 0; i--) C[i] = C[i - 1] * 1ll * fpow(i, mod - 2) % mod;
    C[0] = 0;
    C.resize(n);
    return C;
}
poly quick_pow(poly &a, int k, int k_mod_phi, bool is_k_bigger_than_mod) {
    assert(!a.empty());
    int n = a.size(), t = -1, b;
    for (int i = 0; i < n; i++) if (a[i]) {
        t = i, b = a[i];
        break;
    }
    if (t == -1 || t && is_k_bigger_than_mod || k * 1ll * t >= n) return poly(n, 0);
    poly f;
    for (int i = 0; i < n; i++) {
        if (i + t < n) f.push_back(a[i + t] * 1ll * fpow(b, mod - 2) % mod);
        else f.push_back(0);
    }
    f = ln(f);
    for (auto &x : f) x = x * 1ll * k % mod;
    f = exp(f);
    poly res;
    for (int i = 0; i < k * t; i++) res.push_back(0);
    int fb = fpow(b, k_mod_phi);
    for (int i = k * t; i < n; i++) res.push_back(f[i - k * t] * 1ll * fb % mod);
    return res;
}
```

```cpp
#include <bits/stdc++.h>
using namespace std;

typedef complex<double> cp;
typedef vector<cp> poly;
typedef long long ll;

const int N = 4000000 + 5;
const double pi = acos(-1);

int rf[26][N];
void init(int n) {
    assert(n < N);
    int lg = __lg(n);
    static vector<bool> bt(26, 0);
    if (bt[lg] == 1) return;
    bt[lg] = 1;
    for (int i = 0; i < n; i++) rf[lg][i] = (rf[lg][i >> 1] >> 1) + ((i & 1) ? (n >> 1) : 0);
}
void fft(poly &x, int lim, int op) {
    int lg = __lg(lim);
    for (int i = 0; i < lim; i++) if (i < rf[lg][i]) swap(x[i], x[rf[lg][i]]);
    for (int len = 2; len <= lim; len <<= 1) {
        int k = (len >> 1);
        for (int i = 0; i < lim; i += len) {
            for (int j = 0; j < k; j++) {
                cp w(cos(pi * j / k), op * sin(pi * j / k));
                cp tmp = w * x[i + j + k];
                x[i + j + k] = x[i + j] - tmp;
                x[i + j] = x[i + j] + tmp;
            }
        }
    }
    if (op == -1) for (int i = 0; i < lim; i++) x[i] /= lim;
}
poly multiply(const poly &a, const poly &b) {
    assert(!a.empty() && !b.empty());
    int lim = 1;
    while (lim + 1 < int(a.size() + b.size())) lim <<= 1;
    init(lim);
    poly pa = a, pb = b;
    pa.resize(lim);
    pb.resize(lim);
    for (int i = 0; i < lim; i++) pa[i] = (cp){pa[i].real(), pb[i].real()};
    fft(pa, lim, 1);
    pb[0] = conj(pa[0]);
    for (int i = 1; i < lim; i++) pb[lim - i] = conj(pa[i]);
    for (int i = 0; i < lim; i++) {
        pa[i] = (pa[i] + pb[i]) * (pa[i] - pb[i]) / cp({0, 4});
    }
    fft(pa, lim, -1);
    pa.resize(int(a.size() + b.size()) - 1);
    return pa;
}
vector<int> MTT(const vector<int> &a, const vector<int> &b, const int mod) {
    const int B = (1 << 15) - 1, M = (1 << 15);
    int lim = 1;
    while (lim + 1 < int(a.size() + b.size())) lim <<= 1;
    init(lim);
    poly pa(lim), pb(lim);
    auto get = [](const vector<int>& v, int pos) -> int {
        if (pos >= v.size()) return 0;
        else return v[pos];
    };
    for (int i = 0; i < lim; i++) pa[i] = (cp){get(a, i) >> 15, get(a, i) & B};
    fft(pa, lim, 1);
    pb[0] = conj(pa[0]);
    for (int i = 1; i < lim; i++) pb[lim - i] = conj(pa[i]);
    poly A0(lim), A1(lim);
    for (int i = 0; i < lim; i++) {
        A0[i] = (pa[i] + pb[i]) / (cp){2, 0};
        A1[i] = (pa[i] - pb[i]) / (cp){0, 2}; 
    }
    for (int i = 0; i < lim; i++) pa[i] = (cp){get(b, i) >> 15, get(b, i) & B};
    fft(pa, lim, 1);
    pb[0] = conj(pa[0]);
    for (int i = 1; i < lim; i++) pb[lim - i] = conj(pa[i]);
    poly B0(lim), B1(lim);
    for (int i = 0; i < lim; i++) {
        B0[i] = (pa[i] + pb[i]) / (cp){2, 0};
        B1[i] = (pa[i] - pb[i]) / (cp){0, 2}; 
    }
    for (int i = 0; i < lim; i++) {
        pa[i] = A0[i] * B0[i];
        pb[i] = A0[i] * B1[i];
        A0[i] = pa[i];
        pa[i] = A1[i] * B1[i];
        B1[i] = pb[i];
        B0[i] = A1[i] * B0[i];
        A1[i] = pa[i];
        pa[i] = A0[i] + (cp){0, 1} * A1[i];
        pb[i] = B0[i] + (cp){0, 1} * B1[i];
    }
    fft(pa, lim, -1); fft(pb, lim, -1);
    vector<int> res(int(a.size() + b.size()) - 1);
    const int M2 = M * 1ll * M % mod;
    for (int i = 0; i < res.size(); i++) {
        ll a0 = round(pa[i].real()), a1 = round(pa[i].imag()), b0 = round(pb[i].real()), b1 = round(pb[i].imag());
        a0 %= mod; a1 %= mod; b0 %= mod; b1 %= mod;
        res[i] = (a0 * 1ll * M2 % mod + a1 + (b0 + b1) % mod * 1ll * M % mod) % mod;
    }
    return res;
}
```