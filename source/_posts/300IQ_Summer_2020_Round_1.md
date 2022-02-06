---
title: 300IQ Summer 2020 Round 1
math: true
date: 2020-08-29 14:51:00
categories: 
  - 测试
tags: 
  - 组合数
  - 分治
description: 
picUrl: 
---


# Problem A. Good Subsegments
$$2^{a-l}+2^{a-{l+1}}+...+2^{a-r}=2^x$$
<!--more-->
可以发现,$x\leq max(a-l,a-{l+1}...a-r)+log-2^{r-l+1}$.  
所以固定一个端点时,枚举$x$的值去验证有没有等于$2^x$的区间是可行的.  
但是$2^x$可能很大,高精度很麻烦也会$TLE$,那怎么办呢?  
<!--more-->
可以选一个大质数,然后把$2^{a-l}+2^{a-{l+1}}+...+2^{a-r}=2^x$分别模上它也是相等的.
可以分治来做比较简单,每次找到当前区间的最大的$a-i$,然后枚举$i$左边的区间或者右边的区间(取决于区间长度).  
端点固定后,就枚举$x$,然后去哈希表验证一下有没有这样的另一个端点,如果存在这样的区间,那么就是合法的.  
注意一下细节:
1. 在哈希表中要插入$(key=0,val=0)$,因为区间$[1,r]$可能是合法的,而对应区间的值是$sum-r-sum-0$.  
2. 要选一个尽量大的质数,不然出错的概率很大.哈希表也要选择一个尽量大的,~~比如$114514$~~.  
3. 另外要用快速乘,因为模数是大于$2^{31}-1$的.龟速乘可能会超时吧.  
时间复杂度$\mathcal{O}(nlog^2n)$

```cpp
#include<bits/stdc++.h>
using namespace std;

typedef long long ll;
#define PI pair<ll, int>
const int N = 200005;
const ll P = 4398042316712111199;
int n, a[N], lg[N];
ll sum[N], h[N], ans;
PI mx[N][20];

namespace H {
    const int M = 19260817;
    int hd[M] = {}, tot = 0;
    struct E{int nxt, o; ll v;}e[N];
    void insert(ll h, int o) {int t = ((h % M + M) % M + M) % M, mx = max(t, mx); e[++tot] = E{hd[t], o, h}; hd[t] = tot;}
    int query(ll h) {for(int i = hd[((h % M + M) % M + M) % M]; i; i = e[i].nxt) if(e[i].v == h) return e[i].o; return -1;}
}

ll mul(ll a, ll b) {
    ll c = a * b - (ll)((long double)a * b / P + 0.5) * P;
    return c < 0 ? c + P : c;
}
ll fpow(ll a, ll b) {
    ll ans = 1;
    for(; b; b >>= 1, a = mul(a, a)) if(b & 1) ans = mul(ans, a);
    return ans;
}
void solve(int l, int r) {
    if(l == r) {
        ans += 1;
    } if(l >= r) return;
    int len = lg[r - l + 1], m = max(mx[l][len], mx[r - (1 << len) + 1][len]).second;
    if(r + l >= m + m) {
        for(int i = l; i <= m; i++) {
            ll t = h[m];
            for(int j = 1; j <= 20; j++) {
                ll q = t + sum[i - 1]; if(q >= P) q -= P;
                int p = H::query(q);
                if(p >= m && p <= r) ans++; t *= 2; if(t >= P) t -= P;
            } 
        }
    } else {
        for(int i = m; i <= r; i++) {
            ll t = h[m];
            for(int j = 1; j <= 20; j++) {
                ll q = sum[i] - t; if(q < 0) q += P;
                int p = H::query(q) + 1;
                if(p <= m && p >= l) ans++; t *= 2; if(t >= P) t -= P;
            }
        }
    }
    solve(l, m - 1); solve(m + 1, r);
}

int main() {
    scanf("%d", &n);H::insert(0, 0);
    for(int i = 1; i <= n; i++) scanf("%d", &a[i]);
    for(int i = 2; i <= n; i++) lg[i] = lg[i >> 1] + 1;
    for(int i = 1; i <= n; i++) {
        h[i] = fpow(2, a[i]);
        sum[i] = (sum[i - 1] + h[i]) % P;
        H::insert(sum[i], i);
        mx[i][0] = make-pair(a[i], i);
    }
    for(int j = 1; j <= 19; j++)
        for(int i = 1; i + (1 << j) - 1 <= n; i++) mx[i][j] = max(mx[i][j - 1], mx[i + (1 << j - 1)][j - 1]);
    solve(1, n);
    printf("%lld\n", ans);
    return 0;
}
```

# Problem B. Easy Sum
还没改这道题啊,暂时鸽了.QωQ.  

# Problem C. Funny Cost
据说是最简单的一道...  
题目不太好懂啊,翻译一下就是,给你$N$个元素的序列. 
![Funny Cost](/image/07311.png)    
这个序列每个排列的代价是$n+1$顶点的完美匹配边权和(顶点$u,v$之间的边权是$max-{i=u}^{v-1}a-i$),问你最大代价.  

可以~~暴搜~~发现这样匹配是最优的.  
![匹配方式](/image/07312.png)
怎么计算每个元素的贡献是多少呢?   
$n$是顶点数,考虑每个长度为$\frac{n}{2}$的区间.  
假设现在选出了最大值是第$i$个,那么另外$\frac{n}{2}-1$个顶点要选比它小的值,方案有$\tbinom{i-1}{\frac{n}{2}-1}$种.  
而这个顶点在数组中有$\frac{n}{2}$种放法,区间里的顶点可以任意排列,答案乘上$\frac{n}{2}!$.  
而区间外的顶点也是,有$(\frac{n}{2}-1)!$种排列方法.  
最后再乘上权值.  
答案是$\sum\limits-{i=\frac{n}{2}}^{n}a-i\times \tbinom{i-1}{\frac{n}{2}-1}\times \frac{n}{2}\times \frac{n}{2}! \times (\frac{n}{2}-1)!$

```cpp
#include<bits/stdc++.h>
using namespace std;

const int N = 100005, P = 998244353;
int n, a[N], fac[N], ifac[N];

int C(int n, int m) {
    return fac[n] * 1ll * ifac[m] % P * ifac[n - m] % P;
}

int main() {
    ios::sync-with-stdio(false);
    cin >> n;
    for(int i = 1; i <= n; i++) cin >> a[i];
    fac[0] = ifac[0] = ifac[1] = 1;
    for (int i = 1; i <= n; i++) fac[i] = fac[i - 1] * 1ll * i % P;
    for (int i = 2; i <= n; i++) ifac[i] = (P - P / i) * 1ll * ifac[P % i] % P;
    for (int i = 2; i <= n; i++) ifac[i] = ifac[i - 1] * 1ll * ifac[i] % P;
    sort(a + 1, a + n + 1);
    int m = n / 2 + 1, ans = 0;
    for(int i = m; i <= n; i++) {
        ans = (ans + 1ll * a[i] * C(i - 1, m - 1) % P * m % P * fac[m] % P * fac[m - 1]) % P;
    } 
    cout << ans << endl;
    return 0;
}
```