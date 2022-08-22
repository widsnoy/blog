---
title: Amount of Degrees
mathjax: true
date: 2020-10-14 19:07:19
categories: 
  - 题解
tags: 
  - 动态规划
  - 数位dp
---


>[Amount of Degrees](https://loj.ac/problem/10163)  
这篇博客是来贴代码的，真正的题解看[这里](https://wenku.baidu.com/view/d2414ffe04a1b0717fd5dda8.html)  

```cpp
#include <bits/stdc++.h>
using namespace std;

const int N = 32;
int f[N][N], k, b, l, r, a[N];

int solve(int x) {
	int ans = 0, tot = 0;
	for (int i = 31; i; i--) {
		if (x & (1 << i)) {
            tot++;
            if (tot > k) break;
            x ^= (1 << i);
		}
		if (x >= (1 << (i - 1))) ans += f[i - 1][k - tot];
	}
	if (tot + x == k) ans++;
	return ans;
}

int calc(int x, int b) {
	int cnt = 0;
	while (x) {
		a[cnt++] = x % b;
		x /= b;
	}
	int res = 0;
	for (int i = cnt - 1; i + 1; i--) {
        if (a[i] > 1) {
            for (int j = i; j + 1; j--) res |= (1 << j);
        } else res |= (a[i] << i);
	}
	return res;
}

int main() {
    scanf("%d %d %d %d", &l, &r, &k, &b);
    f[0][0] = 1;
    for (int i = 1; i < N; i++) {
    	f[i][0] = 1;
    	for (int j = 1; j <= i; j++) f[i][j] = f[i - 1][j] + f[i - 1][j - 1];
    }
    printf("%d\n", solve(calc(r, b)) - solve(calc(l - 1, b)));
    return 0;
}
```