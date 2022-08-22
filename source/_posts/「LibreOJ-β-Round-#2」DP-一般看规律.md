---
title: 「LibreOJ β Round #2」DP 一般看规律
mathjax: true
date: 2020-10-20 21:10:31
categories: 
  - 题解
tags: 
  - 数据结构
---


> [「LibreOJ β Round #2」DP 一般看规律](https://loj.ac/problem/516)

感觉题目不是很难，不过代码技巧学习了。  


把所有数按颜色插入到$\text{set}$里面，每次合并的时候对于每个数只有前驱或者后缀会产生答案。  

所以合并的时候更新下答案就可以了。

这个$\text{map<int,set<int>>}$学到了啊，相当于每个下标都对应了一个$\text{set}$，也就是开了多个$\text{set}$，并且是动态申请的。



```cpp
#include <bits/stdc++.h>
using namespace std;

map<int, set<int> > m;
int n, q, ans = 2147483647;

void find(int s, int x) {
    auto it = m[s].lower-bound(x);
    if (it != m[s].end()) ans = min(ans, *it - x);
    if (it != m[s].begin()) ans = min(ans, x - *--it);
}

int main() {
    scanf("%d %d", &n, &q);
    for (int i = 1, x; i <= n; i++) {
    	scanf("%d", &x);
    	m[x].insert(i);
    }
    for (; q--; ) {
    	int x, y;
    	scanf("%d %d", &x, &y);
    	if (x == y) printf("%d\n", ans);
    	else {
            for (auto it = m[x].begin(); it != m[x].end(); ++it) {
            	find(y, *it);
            	m[y].insert(*it);
            }
            m[x].clear();
            printf("%d\n", ans);
    	}
    }
    return 0;
}
```

