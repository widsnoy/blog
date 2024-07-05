---
title: CF868F Yet Another Minimization Problem
mathjax: true
categories:
  - 题解
tags:
  - 动态规划
  - CDQ分治
abbrlink: bc64
date: 2020-09-15 17:24:00
description:
picUrl:
---


写这种题先写一下暴力的$dp$式子.  
$dp[i][k]=min(dp[j][k-1]+w)$.  
可以发现决策是具有单调性的,其他题解说得都很清楚了.  
那么就可以用分治来优化这个方程.  
因为是分层的复杂度是$\mathjaxcal{O}(nlogn)$.  

```cpp
#include<bits/stdc++.h>
using namespace std;

typedef long long ll;
const int N = 1e5 + 5;
int n, p, a[N];
ll f[N][21], m[N];
vector<int> v;

ll value(int L, int R) {
	static int l = 1, r = 0; static ll res = 0;
	while(l > L) l--, res += m[a[l]]++; 
	while(r < R) r++, res += m[a[r]]++;
	while(l < L) res -= --m[a[l]], l++;
	while(r > R) res -= --m[a[r]], r--;
	return res;
}
void CDQ(int l, int r, int tl, int tr, int cur) {
    if(l > r || tl > tr) return;
    int mid = (l + r) >> 1, pos = 0; ll res = 1e18;
    for(int i = tl; i <= tr; i++) {
    	ll v = f[i][cur - 1] + value(i + 1, mid);
    	if(v < res) {
    		res = v; pos = i;
    	}
    }
    f[mid][cur] = min(f[mid][cur], res); CDQ(l, mid - 1, tl, pos, cur); CDQ(mid + 1, r, pos, tr, cur);
}

int main() {
	memset(f, 0x3f, sizeof f); f[0][0] = 0;
    scanf("%d %d", &n, &p);
    for(int i = 1; i <= n; i++) scanf("%d", &a[i]), v.push-back(a[i]);
    sort(v.begin(), v.end());
    v.erase(unique(v.begin(), v.end()), v.end());
    for(int i = 1; i <= n; i++) a[i] = lower-bound(v.begin(), v.end(), a[i]) - v.begin() + 1;
    for(int i = 1; i <= p; i++) CDQ(1, n, 0, n - 1, i);
    printf("%lld\n", f[n][p]);
    return 0;
}
```