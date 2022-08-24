---
title: GCD &amp; XOR
mathjax: true
categories:
  - 题解
tags:
  - 数学
  - 数论
  - gcd
  - xor
abbrlink: 4fa9
date: 2020-09-30 10:17:00
description:
picUrl:
---


>[题目链接](https://loj.ac/problem/6231)  

~~喜提最快解 (?)~~  
## 题目
给定$n,k$
求满足$\gcd(A_{l\cdots r})\cdot(A-l\ \mathjaxrm{xor} \ A_{l+1}\ \mathjaxrm{xor} \cdots \mathjaxrm{xor} \ A-r)=k$且字典序最小的$l,r$   

## 分析
~~可以看我前两篇博客~~  
按照基本方法，枚举右端点$r$  
然后我们需要什么呢  

每个左端点$l$对应的区间$\gcd$和$xor$  
对于$gcd$的维护[看这里](https://widsnoy.top/archives/115/)   

$A-l\ \mathjaxrm{xor} \ A_{l+1}\ \mathjaxrm{xor} \cdots \mathjaxrm{xor} \ A-r$这东西显然是做个异或前缀和  
因为右端点固定，我们显然求$sum[r]\oplus sum[l-1] =\frac{k}{\gcd}$    
异或有个好性质$a\oplus b=c$，则$a\oplus c=b$  

>update:分析没什么问题，代码有点问题，代码删了。
>正确做法还是得记录下每个$xor$所有的位置，二分一个满足要求的位置更新答案。