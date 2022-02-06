---
title: P6713 [CCO2018] Geese vs. Hawks
math: true
date: 2020-09-17 08:41:00
categories: 
  - 题解
tags: 
  - 动态规划
description: 
picUrl: 
---


> 翻译好像没太说清楚啊?QAQ  
<!--more-->
## 题意  
两个人分别在看两支球队比赛, 并且只记录了自己观看一方的胜负和得分.  
现在问你,是否能够从两个人的记录中选出一些合法的比赛,使得分之和最大.  
合法的意思是赢了的一方得分比对方高.  
注意没选的比赛也要合法.(这就是为什么第二个样例没有选$(2,3)$的原因)  

## 题解
并没有要求打印方案,所以有一个很好想的$DP$?  
设$f[i][j]$表示考虑了第一个人的前$i$个记录,第二个人的前$j$个记录时的最优答案.  
转移的时候先令$f[i][j]=max(f[i-1][j],f[i][j-1])$.  
然后如果$(i,j)$合法,$f[i][j]=max(f[i][j],f[i-1][j-1]+a[i]+b[j]$.  
其实就是$LCS$模型吧.

## 代码
```cpp
#include<bits/stdc++.h>
using namespace std;

const int N = 1005;
int n, a[N], b[N];
char S[N], T[N];

int f[N][N];

int dfs(int i, int j) {
    if(i < 0 || j < 0) return 0;
    int& res = f[i][j];
    if (~res) return res;
    if(S[i] != T[j] && ((S[i] == 'W' && a[i] > b[j]) || (T[j] == 'W' && b[j] > a[i]))) res = dfs(i - 1, j - 1) + a[i] + b[j];
    return res = max(res, max(dfs(i - 1, j), dfs(i, j - 1)));
}

int main() {
    memset(f, -1, sizeof f);
    scanf("%d", &n);
    scanf("%s", S);
    for (int i = 0; i < n; i++) scanf("%d", &a[i]);
    scanf("%s", T);
    for (int i = 0; i < n; i++) scanf("%d", &b[i]);
    printf("%d\n", dfs(n - 1, n - 1));
    return 0;
}
```