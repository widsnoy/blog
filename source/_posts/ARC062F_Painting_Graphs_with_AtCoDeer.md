---
title: ARC062F Painting Graphs with AtCoDeer
mathjax: true
categories:
  - 题解
tags:
  - Burnside引理
  - 组合数学
  - 图论
abbrlink: c4cd
date: 2020-08-29 14:55:00
---


# 题目
给定一张$N$个点$M$条边的无向图，每条边要染一个编号在$1$到$K$的颜色。  
你可以对一张染色了的图进行若干次操作，每次操作形如，在图中选择一个简单环（即不经过相同点的环），并且将其颜色逆（顺）时针旋转一个单位。  
两种染色方案被认为是本质相同的，当且仅当其中一种染色后的图经过若干次操作后可以变成另一种染色后的图。  
问有多少本质不同的染色方案，输出对$10^9+7$取模。
![arc062f-1.png][1]


# 题解
首先考虑$n$个点的简单环怎么做。  
根据$Burnside$引理，染色方案有$\frac{1}{n}\sum\limits_{i=0}^{n-1}k^{(n,i)}$种。
那么可以将图的所有点双联通分量单独处理。  
$BCC$缩点后，边分为三种情况。1. 单边  2. 单环  3. 复合环。  
单边就直接算就可以了。  
单环之前考虑过了，其实复合环也很类似。  
![arc062f-2.png][2]
可以发现复合环是可以不改变其他边换掉其中两条边的。  
所以不同的染色方案不同颜色的数量是不同的。  
若每种颜色有$p$个$，p-1+p-2+...+p-k=m$的划分方法就是所有的染色方案。  
用插板法就可解决，方案数是$\binom{n+k-1}{k-1}$。  
最后问题就解决了!时间复杂度是$O(n+2* m)$ 。

# 代码
```cpp
#include<bits/stdc++.h>
using namespace std;

typedef long long ll;
const int N = 105, MOD = 1e9 + 7;
int n, m, k, fac[N << 1], ifac[N << 1];

int fpow(int a, int m) {
	int ans = 1;
	for(; m; m >>= 1) {
		if(m & 1) ans = ans * 1ll * a % MOD;
		a = a * 1ll * a % MOD;
	}
	return ans;
}

struct Edge {
	int nxt, v;
}e[N << 1];
int bccno[N], bcc-cnt,siz-e[N], siz-p[N], dfs-clock, low[N], pre[N], head[N], cnt, top;
pair<int, int> stk[N];

void add(int u, int v) {
	e[++cnt] = (Edge){head[u], v}, head[u] = cnt;
	e[++cnt] = (Edge){head[v], u}, head[v] = cnt;
}
void dfs(int u, int fa) {
    low[u] = pre[u] = ++dfs-clock;
    for(int i = head[u]; ~i; i = e[i].nxt) {
    	int v = e[i].v;
    	if(v == fa) continue;
    	if(!pre[v]) {
    		stk[++top] = make-pair(u, v);
    		dfs(v, u);
    		low[u] = min(low[u], low[v]);
    		if(low[v] >= pre[u]) {
    			bcc-cnt++;
    			while(523) {
                    int x = stk[top].first, y = stk[top].second;
    			    top--;
    			    siz-e[bcc-cnt]++;
    			    if(bccno[x] != bcc-cnt) {bccno[x] = bcc-cnt; siz-p[bcc-cnt]++;}
    			    if(bccno[y] != bcc-cnt) {bccno[y] = bcc-cnt; siz-p[bcc-cnt]++;}
    			    if(x == u && y == v) break;
    			}
    		}
    	}
    	else if(pre[v] < pre[u]) {stk[++top] = make-pair(u, v); low[u] = min(low[u], pre[v]);}
    }
}
int C(int n, int m) {
	return fac[n] * 1ll * ifac[m] % MOD * ifac[n - m] % MOD;
}
int gcd(int a, int b) {
	return b == 0 ? a : gcd(b, a % b);
}

int main() {
    memset(head, -1, sizeof head);
    scanf("%d %d %d", &n, &m, &k);
    fac[0] = ifac[0] = ifac[1] = 1;
    for (int i = 1; i <= m + k; ++i) fac[i] = fac[i - 1] * 1ll * i % MOD;
    for (int i = 2; i <= m + k; ++i) ifac[i] = (MOD - MOD / i) * 1ll * ifac[MOD % i] % MOD;
    for (int i = 2; i <= m + k; ++i) ifac[i] = ifac[i - 1] * 1ll * ifac[i] % MOD;
    for(int i = 1; i <= m; i++) {
    	int u, v;
    	scanf("%d %d", &u, &v);
    	add(u, v);
    }
    for(int i = 1; i <= n; i++) {
    	if(!pre[i]) dfs(i, 0);
    }
    int ans1 = 1, ans2 = 1, ans3 = 1;
    for(int i = 1; i <= bcc-cnt; i++) {
    	if(siz-e[i] == 1) ans1 = ans1 * 1ll * k % MOD;
    	else if(siz-e[i] == siz-p[i]) {
            int sum = 0;
            for(int j = 0; j < siz-e[i]; j++) {
                sum += fpow(k, gcd(siz-p[i], j));
                if(sum >= MOD) sum -= MOD;
            }
            ans2 = ans2 * 1ll * sum % MOD * fpow(siz-e[i], MOD - 2) % MOD; 
    	} else ans3 = ans3 * 1ll * C(siz-e[i] + k - 1, k - 1) % MOD;
    }
    printf("%lld\n", ans1 * 1ll * ans2 % MOD * ans3 % MOD);
    return 0;
}
```


  [1]: https://widsnoy.top/usr/uploads/2020/08/1491481430.png
  [2]: https://widsnoy.top/usr/uploads/2020/08/1048616197.png