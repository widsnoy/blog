---
title: 氯化钠
categories: ACM
tags:
  - 出题
  - 数据结构
mathjax: true
abbrlink: '9420'
date: 2025-02-13 20:51:42
updated:
keywords:
description:
top_img:
comments:
cover:
toc:
toc_number:
toc_style_simple:
copyright:
copyright_author:
copyright_author_href:
copyright_url:
copyright_info:
aplayer:
highlight_shrink:
aside:
---
很高兴能在杭电新生赛出题，这也是我第一次出题，感觉还不错。春季联赛还有一道我的题，敬请期待！其实新生赛已经结束很久，现在将题目公开在我的博客。

题目名字比较奇怪，因为当时某个人宣称 NaCl 是他的 npy，没有其他特别的意思。

> 提交链接：https://www.luogu.com.cn/problem/T544700

## 题目描述

小 y 喜欢在乐扣刷题，某一天做到了这样一个题

> 有一个长度为 $n$ 的数组 $a$，求出数组的所有子段和，并将这 $\frac{n(n+1)}{2}$ 个数降序排列，他想知道第 $k$ 个数有多大。

小 y 很快就解决了这个问题。但是小 y 觉得比起子段和，二元组的和更加美妙，比如 2Na(s)+Cl₂(g)→2NaCl(s)，于是小 y 决定将子段和改成子段中最大值与最小值的和。

小 y 有一个长度为 $n$ 的数组 $a$，定义 $\text{val}(l，r)=\min(a_l,a_{l+1},\dots,a_{r-1},a_r) + \max(a_l,a_{l+1},\dots,a_{r-1},a_r)$。  小 y 想知道对于所有的 $\text{val}(l,r)，1\le l\le r\le n$，降序排列后，第 $k$ 个数是多少，也就是第 $k$ 大的 $\text{val}$。

## 输入格式

第一行一个正整数 $T$ ($T\le200$) 表示数据组数。

对于每组数据：

第一行输入三个整数 $n$ 和 $k$，$(1\le n\le10^5，1\le k\le \frac{n(n+1)}{2})$ 分别表示数组长度，要求第多少大的 $\text{val}$。 

第二行包含 $n$ 个用空格分隔的整数，其中第 $i$ 个数字表示 $a_i$ 的值。 $(0\le a_i\le 10^9)$

保证 $\sum n=1064824$

## 输出格式

对于每组数据，输出一个整数，表示所有的 $\text{val}(l,r)，1\le l\le r\le n$ 中，第 $k$ 大的 $\text{val}$ 的大小。

### 输入 #1

```
2
5 3
1 2 3 3 4
10 10
9 6 7 5 5 4 7 2 5 8
```

### 输出 #1

```
7
13
```

## 题解
题解文件找不到了，我就贴图片吧。
![](/img/nacl_sol.png)


## 赛时情况
比赛时有 5 个老哥 AC 了这道题(1001)，可能是因为码量比较大吧。
![](/img/nacl2.png)
