---
title: LOJ 529 「LibreOJ β Round #5」自然语言
math: true
date: 2020-09-01 21:56:00
categories: 
  - 题解
tags: 
  - 字符串
---


>[题目链接](https://loj.ac/problem/529)

## 题解
简洁的做法是找出合法串的特点，直接判断。  
目的是把串还原成 N 。
<!--more-->
因为端点的 V 一定是由 NV 或者 VN 产生的，所以可以直接消掉。  
同理对于连续的 V 也可以从端点开始消除成为 1 个 V。  
而且连续的 N 一定也是不合法的，数学归纳法可以证明。  

对于英文串，只要不单是 V 就一定合法。  
中文串额外判断一下左端点是不是 N 。

## 代码
```cpp
#include <bits/stdc++.h>
using namespace std;

const int N = 1e6 + 5;
char s[N];

int main() {
	int T;
	scanf("%d", &T);
	while (T--) {
		bool pd1 = 0, pd2 = 0;
		scanf("%s", s + 1);
		int n = strlen(s + 1);
        for (int i = 1; i <= n; i++) {
        	if (s[i] == 'N') pd1 = 1;
        	if (s[i] == 'N' && s[i + 1] == 'N') pd2 = 1;
        }
        if (!pd1 || pd2) puts("0 0");
        else printf("1 %d\n", s[1] == 'N');
	}
	return 0;
}
```