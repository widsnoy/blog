---
title: BZOJ 1034 泡泡堂BNB
math: true
categories:
  - 题解
tags:
  - 贪心
abbrlink: dc90
date: 2020-09-17 15:34:20
description:
picUrl:
---


>[题目链接](https://darkbzoj.tk/problem/1034)  

田忌赛马 
1. 如果最强的能赢最强的，先赢一把
2. 最弱的能赢最弱的，先赢一把  
3. 否则用最弱的打最强的  

```cpp
#include <bits/stdc++.h>
using namespace std;

const int N = 100005;
int n, a[N], b[N];

int solve(int *a, int *b) {
	int l1 = 1, r1 = n, l2 = 1, r2 = n, res = 0;
	while (l1 <= r1 && l2 <= r2) {
		if (a[l1] > b[l2]) res += 2, l1++, l2++;
		else if (a[r1] > b[r2]) res += 2, r1--, r2--;
		else res += (a[l1] == b[r2]), l1++, r2--;
	}
	return res;
}

int main() {
    scanf("%d", &n);
	for (int i = 1; i <= n; i++) scanf("%d", &a[i]);
	for (int i = 1; i <= n; i++) scanf("%d", &b[i]); 
    sort(a + 1, a + 1 + n), sort(b + 1, b + 1 + n);
	printf("%d %d", solve(a, b), n + n - solve(b, a));
    return 0;
}

```