---
title: HDU6061 RXD and functions
categories: 题解
tags:
  - 多项式
mathjax: true
abbrlink: 351f
date: 2024-07-21 18:01:17
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

> 对 $f(x)=\sum\limits_{i=0}^nc_ix^i$ 进行 $m$ 次平移，每次向右平移 $a_i$，问最终得到的表达式 $g(x)$  

画图发现平移 $m$ 次和一次平移 $\sum a_i$ 的结果是一样的，所以只考虑计算一次平移。  

$$
\begin{aligned}
g(x)&=\sum_{i=0}^{n}c_i(x-a)^i\\
&=\sum_{i=0}^{n}c_i\sum_{j=0}^i\binom{i}{j}(-a)^{i-j}x^j\\
&=\sum_{j=0}^n\sum_{i=j}^nc_i\binom{i}{j}(-a)^{i-j}x^j\\
\Rightarrow [x^j]g(x)&=\sum_{i=j}^nc_i\binom{i}{j}(-a)^{i-j}\\
&=\sum_{i=0}^{n-j}c_{i+j}\dfrac{(i+j)!}{i!j!}(-a)^i\\
&=\dfrac{1}{j!}\sum_{i=0}^{n-j}c_{i+j}(i+j)!\dfrac{(-a)^i}{i!}
\end{aligned}
$$

令 $p_i=c_{i}i!$, $b_{n-i}=\dfrac{(-a)^i}{i!}$, 得到 $[x^j]g(x)=\dfrac{1}{j!}\sum\limits_{i=0}^{n-j}p_{i+j}b_{n-i}$，很显然只需要用 NTT 算出 $p*b$ 即可。

这个多测一开始没注意到，太难崩了。。。  
> There are 11 test cases.