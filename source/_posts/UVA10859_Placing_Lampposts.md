---
title: UVA10859 Placing Lampposts
math: true
date: 2020-09-15 18:06:06
categories: 
  - 题解
tags: 
  - 动态规划
  - trick
description: 
picUrl: 
---


>题目链接: [luogu](https://www.luogu.com.cn/problem/UVA10859)

最优化两个目标，好像不太好处理。
可以先转化一下，求被两盏灯同时照亮的边数应该尽可能大，就等价被一盏灯照亮的边数应该尽可能小。
<!--more-->  
假设放了$x$盏灯，被一个灯照的边有$y$条，那么可以设计一个目标函数$g=Mx+y$，让$g$尽量小。$M$是什么？可以想到$M$是一个比较大的数，因为要让$x$的个数对目标函数的大小起决定性作用，可以证明$M$只要比$y-max-y-min$大就可以了。  
那么接下来就考虑一下怎么$dp$，假设现在在节点$u$，$u$的决策就是放灯或者不放灯，这个决策是具有后效性的，怎么办呢？  
我们可以把状态表示为$f[u][j]$，$j=0$表示父节点没放灯，$j=1$则反之。
讨论$u$点放不放灯的两种情况，转移是很容易想到的。  
注意给出的是一个森林，分别计算每棵树的答案就好了。
设答案是$ans$，那么输出就分别是$ans/M$,$m-ans\ mod\ M$,$ans\ mod\ M$。  

```cpp
#include<bits/stdc++.h>
using namespace std;

const int M = 2000, N = 1005;
int n, m, f[N][2], vis[N][2];
vector<int> v[N];

int read() {
	int w = 0; char ch = getchar();
	while(ch > '9' || ch < '0') ch = getchar();
	while(ch <= '9' && ch >= '0') {
		w = w * 10 + ch - 48;
		ch = getchar();
	}
	return w;
}
int dfs(int u, int j, int fa) {
    int &ans = f[u][j], sum = 0;
    if(vis[u][j]) return ans;
    vis[u][j] = 1; ans = M;
    for(int i = 0; i < v[u].size(); i++) { // 放灯
    	int to = v[u][i]; if(to == fa) continue;
    	ans += dfs(to, 1, u);
    }
    if(fa != -1 && !j) ans += 1;
    if(j || fa == -1) {
    	for(int i = 0; i < v[u].size(); i++) {
    		int to = v[u][i]; if(to == fa) continue;
    		sum += dfs(to, 0, u);
    	}
    	if(fa != -1) sum += 1;
        ans = min(ans, sum);
    }
    return ans;
}

int main() {
    int T = read();
    while(T--) {
    	memset(f, 0, sizeof f);
    	memset(vis, 0, sizeof vis);
    	n = read(); m = read();
        for(int i = 0; i < n; i++) v[i].clear();
        for(int i = 1; i <= m; i++) {
        	int u = read(), to = read();
        	v[u].push-back(to); v[to].push-back(u);
        }
        int ans = 0;
        for(int i = 0; i < n; i++) {
        	if(!vis[i][0]) ans += dfs(i, 0, -1);
        }
        printf("%d %d %d\n", ans / M, m - ans % M, ans % M);
    }
    return 0;
}
```