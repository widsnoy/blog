---
title: CF1416C XOR Inverse
math: true
categories:
  - 题解
tags:
  - 01tire
abbrlink: b3f6
date: 2020-09-29 16:28:02
description:
picUrl:
---


>[题目链接](https://codeforces.com/contest/1416/problem/C)  

按位看每一个数。  
如果某两个数是逆序对，那必然是有一个高位不同。  
如果两个数在那一位上面都$\text{xor}$上$1$，两个数的大小关系将会颠倒。  

所以我们可以通过这种方式使逆序对变成顺序对，对于$x$的每一位，我们需要知道有哪些数是从这一位开始不同的，也就是这一位改变会使多少逆序对变成顺序对。  

把所有数都插入$01tire$里面，每个节点记录一下被经过了多少次。  
第一次不同无非就是某个分叉的地方，如果插入的数都是编号更小的，那么我们可以很容易计算出和当前这个数有关的顺序对和逆序对。  

最后每一位都看一下$x$这一位的取值产生的逆序对和顺序对个数。  
贪心选更少的即可。  

```cpp
#include <bits/stdc++.h>
using namespace std;

const int N = (3e5 + 9) * 30;
int n, x, a[N], tr[N][2],  tot; 
long long inv[33], dir[33], s[N];

void insert(const int &x) {
	int u = 0;
	for (int i = 31; i + 1; i--) {
        int c = ((x >> i) & 1);
		if (!tr[u][c]) tr[u][c] = ++tot;
	    u = tr[u][c];
        s[u]++;
	}
}
void qry(const int &x) {
	int u = 0;
	for (int i = 31; i + 1; i--) {
		int c = ((x >> i) & 1);
		if (c == 0) inv[i] += s[tr[u][1]];
		if (c == 1) dir[i] += s[tr[u][0]];
		u = tr[u][c];
	}
}

int main() {
    scanf("%d", &n);
	for (int i = 1; i <= n; i++) scanf("%d", &a[i]), insert(a[i]), qry(a[i]);
	long long res = 0;
	int x = 0;
	for (int i = 0; i <= 31; i++) {
		if (inv[i] > dir[i]) x |= (1 << i), res += dir[i];
		else res += inv[i];
	}
	printf("%lld %d\n", res, x);
	return 0;
}
```