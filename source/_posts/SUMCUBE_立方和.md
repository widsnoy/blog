---
title: SUMCUBE 立方和
mathjax: true
date: 2020-10-20 20:26:55
categories: 
  - 题解
tags: 
  - 图论
  - 组合数学
  - 组合数
  - 三元环
---


> [题目链接](https://s3.amazonaws.com/codechef-shared/download/translated/SEPT17/mandarin/SUMCUBE.pdf)



$k$次方的组合意义是有顺序的选出$k$条边，统计在多少个子图中出现。  


## k=1

考虑每条边的贡献。

答案显然是$m*2^{n-2}$



## k=2

可能选出相同的边，对应$k=1$  

有可能不同，考虑三种情况。  

1. 上

2. 两条边三个点，$A\rightarrow B\rightarrow C$，枚举一条边$(s,t)$，$(deg-s-1)+(deg-t-1)$  
3. 两条边四个点，$A\rightarrow B$,$C\rightarrow D$，总方案数减去上面的，$m^2-①-②$

## k=3

这个比较麻烦，标程是斯特林数去掉指数，很是不懂。

其实也可以大力讨论

1. 三条边重合，即$k=1$
2. 有两条边重合，对应$k=2$
3. 三元环，直接[三元环计数](https://widsnoy.top/archives/76/)
4. 三边四点，$A\rightarrow B\rightarrow C \rightarrow D$或者$A\rightarrow B$, $A\rightarrow C$, $A\rightarrow D$。对于第一种枚举边$(B\\,C)$，$(deg-B-1)\times (deg-C-1)$，A,D可能是一个点，要减去三元环的情况。第二种枚举$A$，$deg-A\times (deg-A-1)\times (deg-A-2)$。
5. 三边五点， 可能有一种情况$A\rightarrow B\rightarrow C$,$D\rightarrow E$。枚举$B$点，有$deg-B\times(deg-B-1)\times (m-2)$种方法。但是有可能算到三元环和三边四点的情况。这里注意对于三边四点的第一种情况会算两次，第二种情况算三次。
6. 三边两点，$m\times (m-1)\times (m-2)$是有序选不重边的所有方案，减去③④⑤就是这个。

上面的所有选出的情况都是不考虑顺序的，选不重的边最后应该乘上$6$表示选取顺序。



```cpp
#include <bits/stdc++.h>
using namespace std;

const int N = 1e5 + 5, MOD = 1e9 + 7;
int n, m, k, deg[N];

int fpow(int a, int b) {
    if (b < 0) return 0;
    int res = 1;
    for (; b; b >>= 1, a = a * 1ll * a % MOD) if (b & 1) res = res * 1ll * a % MOD;
    return res;
}

namespace baoli {
    bool b[20][20];
    vector<int> e[20];
    int F(int s) {
        memset(b, 0, sizeof b);
        int res = 0;
        for (int i = 1; i <= n; i++) {
            if ((1 << (i - 1)) & s) {
                for (int j : e[i]) {
                    if (!b[i][j] && (1 << (j - 1) & s)) res = (res + 1) % MOD, b[i][j] = b[j][i] = 1;
                }
            }
        }
        return res;
    }
    void main() {
        for (int i = 1; i <= n; i++) e[i].clear();
        for (int i = 1; i <= m; i++) {
            int u, v;
            scanf("%d %d", &u, &v);
            e[u].push-back(v);
            e[v].push-back(u);
        }
        int res = 0;
        for (int i = 1; i <= (1 << n) - 1; i++) res = (res + fpow(F(i), k)) % MOD;
        printf("%d\n", res);
        exit(0);
    }
}
namespace kk {
    vector<int> e[N];
    int vis[N];
    struct node {
        int u, v;
    };
    node v[N];
    int calc() {
        int res = 0;
        for (int i = 1; i <= m; i++) {
            if (deg[v[i].u] > deg[v[i].v] || (deg[v[i].u] == deg[v[i].v] && v[i].u < v[i].v)) e[v[i].u].push-back(v[i].v);
            else e[v[i].v].push-back(v[i].u);
        }
        for (int i = 1; i <= n; i++) {
            for (auto v : e[i]) vis[v] = i;
            for (auto v : e[i])
                for (auto j : e[v]) if (vis[j] == i) res++;
        }
        return res;
    }
    void main() {
        for (int i = 1; i <= m; i++) {
            scanf("%d %d", &v[i].u, &v[i].v);
            deg[v[i].u]++;
            deg[v[i].v]++;
        }
        int y3 = calc();
       // printf("y3 = %d\n", y3);
        int res1 = m * 1ll * fpow(2, n - 2) % MOD;
        int res = 0;
        long long tmp = 1ll * m * (m - 1) / 2;
        for (int i = 1; i <= n; i++) {
            int w = deg[i];
            if (n >= 3) res = (res + 1ll * w * (w - 1) / 2 % MOD * fpow(2, n - 3)) % MOD;
            tmp -= 1ll * w * (w - 1) / 2;
        }
        if (n >= 4) res = (res + tmp % MOD * fpow(2, n - 4)) % MOD;
        res= res * 2ll % MOD;
        res = (res + m * 1ll * fpow(2, n - 2) % MOD) % MOD;
        int ans = (res + MOD - res1) % MOD;
        ans = ans * 3ll % MOD;
        ans = ans + res1 % MOD;
        const int i6 = fpow(6, MOD - 2), i2 = fpow(2, MOD - 2);
        int cnt4 = MOD - 3 * 1ll * y3 % MOD, cnt5 = 0, cnt6 = 0;
        for (int i = 1; i <= n; i++) {
            cnt4 = (cnt4 + deg[i] * 1ll * (deg[i] - 1) % MOD * (deg[i] - 2) % MOD * 1ll * i6 % MOD) % MOD;
        }
        for (int i = 1; i <= m; i++) {
            cnt4 = (cnt4 + (deg[v[i].u] - 1) * 1ll * (deg[v[i].v] - 1) % MOD);
        }
        cnt5 = MOD - cnt4 * 2ll % MOD;
        cnt5 = (cnt5 + MOD - y3 * 1ll * 3 % MOD) % MOD;
        for (int i = 1; i <= n; i++) {
            cnt5 = (cnt5 + MOD - deg[i] * 1ll * (deg[i] - 1) % MOD * (deg[i] - 2) % MOD * 1ll * i6 % MOD) % MOD;
        }
        for (int i = 1; i <= n; i++) {
            cnt5 = (cnt5 + (deg[i] - 1) * 1ll * deg[i] % MOD * 1ll * (m - 2) % MOD * i2 % MOD) % MOD;
        }
        cnt6 = (m * 1ll * (m - 1) * 1ll * (m - 2) / 6 - y3 - cnt4 - cnt5) % MOD;
        printf("%lld\n", (ans + 6ll * ((y3 * 1ll * fpow(2, n - 3) % MOD + cnt4 * 1ll * fpow(2, n - 4) % MOD + cnt5 * 1ll * fpow(2, n - 5) % MOD + cnt6 * 1ll * fpow(2, n - 6) % MOD) % MOD) % MOD) % MOD);
        exit(0);
    }
}

int main() {
    scanf("%d %d %d", &n, &m, &k);
    if (n <= 15) baoli::main();
    if (k == 3) kk::main();
    for (int i = 1; i <= m; i++) {
        int u, v;
        scanf("%d %d", &u, &v);
        deg[u]++, deg[v]++;
    }
    if (k == 1) printf("%lld\n", m * 1ll * fpow(2, n - 2) % MOD);
    else if (k == 2) {
        int res = 0;
        long long tmp = 1ll * m * (m - 1) / 2;
        for (int i = 1; i <= n; i++) {
            int w = deg[i];
            if (n >= 3) res = (res + 1ll * w * (w - 1) / 2 % MOD * fpow(2, n - 3)) % MOD;
            tmp -= 1ll * w * (w - 1) / 2;
        }
        if (n >= 4) res = (res + tmp % MOD * fpow(2, n - 4)) % MOD;
        res= res * 2ll % MOD;
        res = (res + m * 1ll * fpow(2, n - 2) % MOD) % MOD;
        printf("%d\n", res);
    }
    return 0;
}
```

