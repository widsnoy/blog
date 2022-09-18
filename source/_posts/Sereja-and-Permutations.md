---
layout: posts
title: Sereja and Permutations
date: 2022-09-18 23:36:23
categories: 题解
tags: 
- 哈希
- 树状数组
math: true
---

[SEAPERM2.pdf](https://s3.amazonaws.com/codechef_shared/download/translated/NOV14/mandarin/SEAPERM2.pdf)  
## $O(n^5)$
因为总有一个排列是由删除$p_1$得来的，所以可以枚举之并还原每个$n-1$的排列。得到$n^2$个可能的 P。 然后暴力验证每一个。

## $O(n^4)$
验证的时候比对排列的哈希值，哈希不必担心碰撞问题，因为恰好不同排列的哈希值对应交换才会产生影响，这个几率应该很小.......  
[不知道用什么模数？](https://zh.numberempire.com/233333)

## $O(n^3)$
可以发现，输入的$n-1$的排列的第一个数一定有$p_1-1$个$p_1-1$，$1$个$p_2$，$n-p_1$个$p_1$。所以我们是可以知道$p_1$的取值的。

## $O(n^2\log n)$
如果使用可加减的哈希，那么可以$O(n\log n)$求出一个原排列操作后的$n$个$n-1$的排列的哈希值。
```cpp
#include <bits/stdc++.h>
using namespace std;

typedef long long i64;
const int mod = 233329, b = 17, inv = 54901;
const int N = 305;

int n, a[N][N], p[N], h0[mod + 5], h1[mod + 5], ba[N];

int t[N];

int lowbit(int x) { return x & -x; }
void add(int x, int k) {
    for (int i = x; i <= n; i += lowbit(i)) t[i] += k, t[i] %= mod;
}
int sum(int x) {
    int ans = 0;
    for (int i = x; i; i -= lowbit(i)) ans += t[i], ans %= mod;
    return ans;
}
int qry(int l, int r) { return l <= r ? ((sum(r) - sum(l - 1)) % mod + mod ) % mod : 0; }

int f[N], g[N], h[mod + 5];

bool check() {
    for (int i = 1; i <= n; i++) {
        h[i] = (h[i - 1] + ba[n - i] * 1ll * p[i] % mod) % mod;
    }
    memset(t, 0, sizeof t);
    for (int i = 1; i <= n; i++) {
        f[i] = qry(p[i] + 1, n);
        add(p[i], ba[n - i]);
    }
    memset(t, 0, sizeof t);
    for (int i = n; i >= 1; i--) {
        g[i] = qry(p[i] + 1, n);
        add(p[i], ba[n - i]);
    }
    auto dec = [](int a, int b) { return ((a - b) % mod + mod) % mod; };
    memcpy(h1, h0, sizeof h1);
    for (int i = 1; i <= n; i++) {
        static int res = 0;
        res = (1ll * dec(h[i - 1], f[i]) * inv % mod + dec(dec(h[n], h[i]), g[i])) % mod;
        if (!h1[res]) return 0;
        h1[res]--;
    }
    return 1;
}

bool gen(int p1) {
    for (int i = 1; i <= n; i++) {
        p[1] = p1;
        for (int j = 1; j < n; j++) p[j + 1] = a[i][j] + (a[i][j] >= p1);
        if (check()) return 1;
    }
    return 0;
}

void solve() {
    scanf("%d", &n);
    memset(h0, 0, sizeof h0);
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j < n; j++) scanf("%d", &a[i][j]);
        int res = 0;
        for (int j = 1; j < n; j++) res = (res * 1ll * b % mod  + a[i][j]) % mod;
        h0[res]++;
    }
    static bool pd[N];
    memset(pd, 0, sizeof pd);
    for (int i = 1; i <= n; i++) if (!pd[a[i][1]]) {
        pd[a[i][1]] = 1;
        if (gen(a[i][1])) {
            for (int i = 1; i <= n; i++) printf("%d ", p[i]);
            return printf("\n"), void(0);
        }
    }
    if (gen(n)) {
        for (int i = 1; i <= n; i++) printf("%d ", p[i]);
        return printf("\n"), void(0);
    }
}

int main() {
    //freopen("in.txt", "r", stdin);
    ba[0] = 1;
    for (int i = 1; i < N; i++) ba[i] = ba[i - 1] * b % mod;
    int T;
    scanf("%d", &T);
    while (T--) solve();
    return 0;
}
```