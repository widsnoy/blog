---
title: UVa1642 魔法gcd Magical gcd
mathjax: true
date: 2020-09-30 09:15:14
categories: 
  - 题解
tags: 
  - gcd
  - 数论
description: 
picUrl: 
---


题目： luogu: [UVa1642](https://www.luogu.com.cn/problem/UVA1642)
# solution

因为要求一个最优的子序列，可以想到枚举这个子序列的右端点$j$。  那么怎么快速算出左端点$i$的答案呢？  
枚举每一个左端点，如果能知道这个子序列所有元素的$gcd$值就好了。  
先考虑这样一个序列$$5,8,8,6,2$$，假设现在$j=4$,可以算出所有子序列对应的$gcd$。
1. $i=1$, $gcd(a-1,a-2,a-3,a-4)=1$
2. $i=2$, $gcd(a-2,a-3,a-4)=2$
3. $i=3$, $gcd(a-3,a-4)=2$
4. $i=4$, $gcd(a-4)=6$

注意到不同子序列的$gcd$值有可能是相等的，事实上$gcd$值的种类最多不会超过$\log-2 a-j$个，因为$a-j$的约数个数一定不多于$\log-2 a-j$。  
上表从下向上看，每次增加一个元素的时候，$gcd$值是不变或者减小的，而且变小时一定会变成$a-j$的一个约数。所以就维护每个左端点对应的区间$gcd$值(即$gcd(a[i],a[i+1],...,a[j])$)，增加一个元素(即$j$ -> $j+1$)时，更新加上该元素后的区间$gcd$值就可以。  
知道了当前每个左端点对应的$gcd$，区间长度也是已知的，就可以计算答案了，对于每个区间的结果取一个最大值。  
但是直接这样做复杂度是不对的，$O(n^2log n)$显然过不了。那怎么办呢。
我们可以发现，如果两个左端点的$gcd$相等，那么$i$更小的一定会更优，所以直接把劣的删除，对后面不造成影响。这样一来每次要枚举的$i$就和$gcd$的个数有关，复杂度是$O(nlog^2 n)$，可以通过本题。

# code

```cpp
#include<bits/stdc++.h>
using namespace std;

typedef long long ll;
const int N = 1e5 + 5;
ll n, a[N];
vector<pair<int, ll> > v;

ll read() {
	ll w = 0; char ch = getchar();
	while(ch > '9' || ch < '0') ch = getchar();
	while(ch >= '0' && ch <= '9') {
		w = w * 10 + ch - 48;
		ch = getchar();
	}
	return w;
}
ll gcd(ll a, ll b) {
	return b == 0 ? a : gcd(b, a % b);
}

int main() {
    ll T = read();
    while(T--) {
    	ll ans = 0; v.clear();
    	n = read();
    	for(int i = 1; i <= n; i++) a[i] = read();
        for(int j = 1; j <= n; j++) {
            v.push-back({j, a[j]});
            for(int i = v.size() - 2; i >=0; i--) {
            	v[i].second = gcd(v[i].second, a[j]);
            	if(v[i].second == v[i + 1].second) v.erase(v.begin() + i + 1);
            }
            for(int i = 0; i < v.size(); i++) {
            	ans = max(ans, (j - v[i].first + 1) * (v[i].second));
            }
        }
        printf("%lld\n", ans);
    }
    return 0;
}
```