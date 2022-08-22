---
title: UVA10817 Headmaster's Headache
mathjax: true
date: 2020-09-07 14:59:00
categories: 
  - 题解
tags: 
  - 动态规划
---


>[题目链接](https://vjudge.net/problem/UVA-10817)  

读入方式很新颖啊  
把没人教的科目，只有一个人教的科目，和两个人教的科目状压一下。  
设状态$f[i][s0][s1][s2]$为当前考虑了前$i$人每个科目的状况，还需要的最小花费。  
每个人无非选和不选两种决策，记搜一下。  
转移从后面一个人转移过来，好像有点诡异。

这道题好像很难正向转移的样子，因为 s2 表示的其实是大于等于 2 人的科目，所以正着转移很难表示旧的 s2。  
而考虑还剩多少钱，可以发现 s2 的科目是不减的，所以这样的状态就很方便 

```cpp
#include <bits/stdc++.h>
using namespace std;

const int N = 125, M = (1 << 8) + 5;
int n, m, s, S[N], C[N], f[N][M][M];

int dfs(int x, int s0, int s1, int s2) {
	if (x == n + m) return s2 == (1 << s) - 1 ? 0 : 1000000000;
	int &ans = f[x][s1][s2];
	if (ans != -1) return ans;
	ans = 1000000000;
	if (x >= m) ans = min(ans ,dfs(x + 1, s0, s1, s2));
    int m0 = S[x] & s0, m1 = S[x] & s1;
    s0 ^= m0; s1 ^= m1; s1 |= m0; s2 |= m1;
    ans = min(ans, dfs(x + 1, s0, s1, s2) + C[x]);
    return ans;
}

int main() {
//	freopen("test.in", "r", stdin);
//	freopen("test.out", "w", stdout);
	int x;
    string line;
    while (getline(cin, line)) {
    	stringstream ss(line);
    	ss >> s >> m >> n;
    	if (s == 0 && m == 0 && n == 0) break;
    	for (int i = 0; i < n + m; i++) {
    		getline(cin, line);
    		stringstream ss(line);
    		ss >> C[i];
    		S[i] = 0;
    		while (ss >> x) S[i] = (S[i] | (1 <<(x - 1)));
    	}
    	memset(f, -1, sizeof f);
        printf("%d\n", dfs(0, (1 << s) - 1, 0, 0));
    }
    return 0;
}
```