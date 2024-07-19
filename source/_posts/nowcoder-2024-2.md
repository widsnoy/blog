---
title: nowcoder_2024_2
categories: ACM
tags:
  - 多校
mathjax: true
abbrlink: '2130'
date: 2024-07-19 18:29:31
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
## Taking Candies 
> Emofunc 和 Cnufome 进行 $n$ 轮拍卖。两人初始时候分别有 $x$，$y$ 枚金币，每次 Emofunc 先手，成功拍卖的人将钱给对方，然后从 $n$ 件物品中随意选择一个拿走。如果两个人都执行最优策略，Emofunc 能得到的物品价值和最大是多少？  $n\le1e5,x,y\le 100$  

观察到金币数很少，尝试用它来划分状态。$f[n][x][y]$ 表示还剩下 $n$ 个物品，Emofunc 和 Cnufome 分别有 $x$，$y$ 枚金币的情况下，Emofunc 先手最多能得到多大价值和的物品。  

枚举 Emofunc 提出多大的竞拍价格 $t$，能获得的收益是 $\min\limits_{t'>t}(f[n-1][x-t][y+t]+a[n],f[n-1][x+t][y-t])$。取 min 是因为 Cnufome 可以提出一个更高的竞拍价格来最小化 Emofunc 的收益。只需要考虑一轮是因为，如果考虑多轮，发现是和只考虑一轮一样的问题。  

发现 $x+y$ 是定值，所以可以压缩一维状态。而且对于同一个 $n$，$f[n][x]$ 显然关于 $x$ 单调。因为钱更多肯定在拍卖中占优势。  

```cpp
for (int i = 1; i <= n; i++) {
    for (int x = 0; x <= xy; x++) {
        for (int t = 0; t <= x; t++) {
            ll res = dp[i - 1][x - t] + a[i];
            if (x + t + 1 <= xy) res = min(res, dp[i - 1][x + t + 1]);
            dp[i][x] = max(dp[i][x], res);
        }
    }
}
```
![](/img/TakingCandies.png)

考虑怎么优化 $t$ 那一层循环，发现如果某个 $t$ 满足 $dp[i - 1][x - t] + a[i]<dp[i - 1][x + t + 1]$ 那么更大的 $t$ 也满足，可以直接二分出转折点，并且我们还发现最优的转移点一定是转折点相邻的两个点！  

```cpp
#include <bits/stdc++.h>
//#include <ext/pb_ds/assoc_container.hpp>
//#include <ext/pb_ds/tree_policy.hpp>
//using namespace __gnu_pbds;
using namespace std;

#define fi first
#define se second
typedef pair<int, int> pii;
typedef long long ll;
typedef long double ld;
//mt19937_64 rng(std::chrono::steady_clock::now().time_since_epoch().count());

const int mod = 998244353;
const int N = 1e5 + 5;

ll dp[N][201];
int n, a[N], xy;
ll sum[N];

void MAIN() {
    int n, x, y;
    cin >> n >> x >> y;
    xy = x + y;
    for (int i = 1; i <= n; i++) cin >> a[i];
    sort(a + 1, a + n + 1);
    for (int i = 1; i <= n; i++) {
        for (int x = 0; x <= xy; x++) {
            int l = 0, r = x, ans = -1;
            while (l <= r) {
                int mid = (l + r) >> 1;
                if (x + mid + 1 > xy || dp[i - 1][x + mid + 1] > dp[i - 1][x - mid] + a[i]) r = mid - 1, ans = mid;
                else l = mid + 1;
            }
            if (ans == -1) dp[i][x] = dp[i - 1][x + x + 1];
            else dp[i][x] = max(dp[i - 1][x - ans] + a[i], dp[i - 1][x + ans]);
        }
    }
    cout << dp[n][x] << '\n';
}

int main() {
    //int size(512 << 20); // 512M
    //__asm__("movq %0, %%rsp
    ios::sync_with_stdio(0); cin.tie(0);
    int T = 1;
    // cin >> T;
    while (T--) MAIN();
    return 0;
}
```

## The Set of Squares
> 有一个多重集 $S$。定义集合的权值，若元素的乘积是完全平方数 $x$，权值为 $\sqrt x$，否则权值为 $0$。求 $S$ 的所有非空子集权值和。$\forall x\in S,1\le x\le 1000$

~~比赛的时候看了一下，写下了 $\prod(a_i+1)$ 的神秘式子，感觉是不可做题就跑了。看到题解说按大质数分组背包瞬间就懂了 233~~

完全平方数也就是每个质数出现了偶数次。因为数字比较小，小质数的奇偶性直接状压，大质数分组之后单独统计。再做一个类似卷积的东西合并所有分组。

```cpp
//#pragma GCC optimize(2,"Ofast","inline")
#include <bits/stdc++.h>
//#include <ext/pb_ds/assoc_container.hpp>
//#include <ext/pb_ds/tree_policy.hpp>
//using namespace __gnu_pbds;
using namespace std;

#define fi first
#define se second
typedef pair<int, int> pii;
typedef long long ll;
typedef long double ld;
//mt19937_64 rng(std::chrono::steady_clock::now().time_since_epoch().count());

const int mod = 1e9 + 7;
const int N = 1000 + 5, M = 12;
int pri[12] = {2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31};
int n, a[N], ind[35];
int f[N][(1 << M) + 5][2], pd[(1 << M) + 5], g[(1 << M) + 5], nxt[(1 << M) + 5];

struct solve {
    int P;
    vector<int> a, b;
    void main() {
        int n = a.size();
        f[0][0][0] = 1;
        int mask = (1 << 11) - 1;
        for (int i = 0; i < n; i++) {
            int y = a[i], x = b[i];
            memcpy(f[i + 1], f[i], sizeof f[i + 1]);
            for (int j = 0; j <= mask; j++) {
                for (int k = 0; k <= 1; k++) if (f[i][j][k]) {
                    f[i + 1][j ^ y][k ^ 1] = 
                        (f[i + 1][j ^ y][k ^ 1] + 
                        f[i][j][k] * 1ll * pd[j & y] % mod 
                        * (k == 1 ? P : 1) % mod * x % mod) % mod;
                    //cerr << i << ' ' << j << ' ' << k << ' ' << f[i][j][k] << '\n';
                }
            }
        }
        memcpy(nxt, g, sizeof nxt);
        nxt[0] = (nxt[0] + mod - 1) % mod;
        for (int j = 0; j <= mask; j++) {
            for (int i = 0; i <= mask; i++) {
                nxt[i ^ j] = (nxt[i ^ j] + g[i] * 1ll * f[n][j][0] % mod * pd[i & j] % mod) % mod;
            }
        }
        memcpy(g, nxt, sizeof g);
        if (P == 1) {
            memcpy(nxt, g, sizeof nxt);
            for (int j = 0; j <= mask; j++) {
                for (int i = 0; i <= mask; i++) {
                    nxt[i ^ j] = (nxt[i ^ j] + g[i] * 1ll * f[n][j][1] % mod * pd[i & j] % mod) % mod;
                }
            }
            memcpy(g, nxt, sizeof g);
        }
        for (int i = 0; i <= n; i++) memset(f[i], 0, sizeof f[i]);
    }
};
solve so[N];

void MAIN() {
    g[0] = 1;
    cin >> n;
    for (int i = 0; i < M; i++) ind[pri[i]] = i;
    int mul = 1, mask = (1 << 11) - 1;
    for (int i = 0; i <= mask; i++) {
        pd[i] = 1;
        for (int j = 0; j < 11; j++) if ((i >> j) & 1) {
            pd[i] = pd[i] * 1ll * pri[j] % mod;
        }
    }
    for (int i = 1; i <= n; i++) {
        cin >> a[i];
        if (a[i] == 1) {mul = mul * 2 % mod; continue;}
        int b = 0, w = 1;
        for (int j = 2; j * j <= a[i]; j++) {
            if (a[i] % j != 0) continue;
            int cnt = 0;
            while (a[i] % j == 0) {
                b ^= (1 << (ind[j]));
                a[i] /= j;
                cnt ^= 1;
                if (!cnt) w *= j;
            }
        }
        if (a[i] != 1 && a[i] < 32) {
            b ^= (1 << (ind[a[i]]));
            a[i] = 1;
        }
        so[a[i]].a.push_back(b);
        so[a[i]].b.push_back(w);
    }
    for (int i = 1; i <= 1000; i++) if (!so[i].a.empty()) {
        so[i].P = i;
        so[i].main();
    }
    //cerr << g[0] << '\n';
    int res = (g[0] + mod - 1) % mod;
    res = (res * 1ll * mul % mod + mul - 1) % mod;
    res = (mod + res) % mod;
    cout << res << '\n';
}

int main() {
    //int size(512 << 20); // 512M
    //__asm__("movq %0, %%rsp
    ios::sync_with_stdio(0); cin.tie(0);
    int T = 1;
    // cin >> T;
    while (T--) MAIN();
    return 0;
}
```