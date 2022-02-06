---
title: LOJ10059 Censoring
math: true
date: 2020-09-26 18:03:19
categories: 
  - 题解
tags: 
  - 字符串
  - AC自动机
description: 
picUrl: 
---


>[题目链接](https://loj.ac/problem/10059)  
<!--more-->
维护一个栈，表示每个输出字符对应$tire$图上的节点，在$tire$图上游走时，如果走到某个节点是敏感词结束的地方，就弹出敏感词，并且栈回退到到这个敏感词之前的位置。

```cpp
#include <bits/stdc++.h>
using namespace std;

const int N = 1e6 + 5;
int n, tr[N][26], fail[N], id[N], tot, stk[N], top, o[N];
char s[N], qaq[N];

void insert(const char *s) {
	int u = 0, n = strlen(s + 1);
	for (int i = 1; i <= n; i++) {
		int c = s[i] - 'a';
		if (!tr[u][c]) tr[u][c] = ++tot;
		u = tr[u][c];
	}
	id[u] = n;
}
void build() {
	queue<int> q;
	for (int i = 0; i < 26; i++) if (tr[0][i]) q.push(tr[0][i]);
	while (!q.empty()) {
		int u = q.front(); q.pop();
		for (int i = 0; i < 26; i++) {
			if (tr[u][i]) fail[tr[u][i]] = tr[fail[u]][i], q.push(tr[u][i]);
			else tr[u][i] = tr[fail[u]][i];
		}
	}
}

int main() {
	scanf("%s", qaq + 1);
	scanf("%d", &n);
	for (int i = 1; i <= n; i++) scanf("%s", s + 1), insert(s);
	build();
	int m = strlen(qaq + 1), u = 0;
	for (int i = 1; i <= m; i++) {
		int c = qaq[i] - 'a';
		u = tr[u][c];
		stk[++top] = u;
		o[top] = i;
		if (id[u]) {
			top -= id[u];
			u = stk[top];
		}
	}
	for (int i = 1; i <= top; i++) printf("%c", qaq[o[i]]);
	return 0;
}
```
