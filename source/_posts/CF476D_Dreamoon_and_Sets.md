---
title: CF476D Dreamoon and Sets
mathjax: true
date: 2020-09-15 17:22:25
categories: 
  - 题解
tags: 
  - 构造
description: 
picUrl: 
---


构造。
因为四元组最大公因数是$k$，所以将四个数同时除以$k$，得到的四个数互质。  
所以构造出四个互质的数就可以了。  
设第一个数是$x$，因为四元组尽量小，所以构造的数要尽量接近。  
显然只能有一个偶数，所以很容易得到第二个数是$x+1$，第三个数是$x+2$，并且$2\nmid x$。  
第四个数不能是$x+3$，因为只能有一个偶数。那么可不可以是$x+4$呢？  
我们知道连续$n$个数一定有一个能被$n$整除，因为$2\nmid x$，所以是可以的。  
注意一下每个数只能在答案中出现一次，找下规律就可以了。
```c++
#include<cstdio>
using namespace std;

int n, k;

int main() {
    scanf("%d %d",&n, &k);
    printf("%d\n",( 6 * n - 1) * k);
    for(int i = 1; i <= n; ++i) {
        int m1 = (i - 1) * 6 + 1;
        printf("%d %d %d %d\n", m1 * k, (m1+1) * k, (m1+2) * k, (m1+4) * k);
    }
    return 0;
}
```