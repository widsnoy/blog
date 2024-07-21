---
title: polynomial 学习笔记
categories: ACM
tags:
  - 多项式
mathjax: true
abbrlink: '7026'
date: 2024-07-20 14:59:53
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
> 多项式模板: https://github.com/widsnoy/algorithms/blob/main/%E5%A4%9A%E9%A1%B9%E5%BC%8F/poly.cpp

## 生成函数
- 常用封闭形式
1.  $a=\langle 0,1,1,1,1,\cdots\rangle \rightarrow \dfrac{x}{1-x}$  

2.  $a=\langle 1,0,1,0,1,\cdots \rangle$   
    $$
    \begin{aligned}
    F(x)&=\sum_{n\ge 0}x^{2n}\\
    &=\sum_{n\ge 0}(x^2)^{n}\\
    &=\frac{1}{1-x^2}
    \end{aligned}
    $$

3.  $a=\langle 1,2,3,4,\cdots \rangle$ 
$$\begin{aligned}F(x)&=\sum_{n\ge 0}(n+1)x^n\\&=\sum_{n\ge 1}nx^{n-1}\\&=\sum_{n\ge 0}(x^n)'\\&=\left(\frac{1}{1-x}\right)'\\&=\frac{1}{(1-x)^2}\end{aligned}$$
4.  $a_n=\binom{m}{n}$（$m$ 是常数，$n\ge 0$）
$$ F(x)=\sum_{n\ge 0}\binom{m}{n}x^n=(1+x)^m$$  

5.  $a_n=\binom{m+n}{n}$（$m$ 是常数，$n\ge 0$）
$$F(x)=\sum_{n\ge 0}\binom{m+n}{n}x^n=\frac{1}{(1-x)^{m+1}}$$

- 牛顿二项式定理

我们重新定义组合数的运算：

$$
\binom{r}{k}=\frac{r^{\underline{k}}}{k!}\quad(r\in\mathbf{C},k\in\mathbf{N})
$$

注意 $r$ 的范围是复数域。在这种情况下。对于 $\alpha\in\mathbf{C}$，有

$$
(1+x)^{\alpha}=\sum_{n\ge 0}\binom{\alpha}{n}x^n
$$

二项式定理其实是牛顿二项式定理的一个特殊情况。

- 例题: [BZOJ 3028 食物](https://new.bzoj.org:88/p/P3028)

先写出 $8$ 种食物各自方案数的生成函数
1. $\dfrac{1}{1-x^2}$  
2. $1+x$
3. $1+x+x^2$
4. $\dfrac{x}{1-x^2}$
5. $\dfrac{1}{1-x^4}$
6. $1+x+x^2+x^3$
7. $1+x$
8. $\dfrac{1}{1-x^3}$

总的方案的生成函数是各自生产函数的卷积，封闭形式是 $F(x)=\dfrac{x}{(1-x)^4}$  
设 $G(x)=\sum\limits_{n\ge 0}\binom{n+3}{n}x^n=\dfrac{1}{(1-x)^4}$  
$
F(x)=xG(x)=\sum_{n\ge 1}\binom{n+2}{n-1}x^n
$

## 多项式科技
### 分治 FFT
“分治 + FFT”  

形如 $\prod 1+x^i$ 的柿子，直接分治算复杂度为 $O(NlogN)$

### 半在线卷积

给定序列 $g_{1\dots n - 1}$，求序列 $f_{0\dots n - 1}$。

其中 $f_i=\sum_{j=1}^if_{i-j}g_j$，边界为 $f_0=1$。

CDQ 分治考虑 $[l,mid]$ 对 $(mid,r]$ 贡献。  

$
\begin{aligned}
w_x&=\sum\limits_{i=l}^{mid}f_i g_{x-i}\\
&=\sum\limits_{i=0}^{mid-l}f_{i+l}g_{x-i-l}\\
&=\sum\limits_{i=0}^{x-l-1}f_{i+l}g_{x-i-l}\\
\end{aligned}
$

第三步是因为 $f_{mid+1\dots r}=0$。令 $a_i=f_{i+l}$, $b_i=g_{i+1}$, 原式等于 $w_x=\sum\limits_{i=0}^{x-l-1}a_i b_{x-i-l-1}$
### 多形式求逆  

给定一个多项式 $F(x)$ ，请求出一个多项式 $G(x)$， 满足 $F(x) * G(x) \equiv 1 \pmod{x^n}$。  

显然有 $G(0)=\dfrac{1}{F(0)}$，$\forall n\ge 1, \ \sum\limits_{i=0}^nF(i)G(n-i)=0 \Rightarrow F(n)=\dfrac{-1}{G(0)}\sum\limits_{i=0}^{n-1}F(i)G(n-i)$  于是可以 $O(n^2)$ 递推。  

- 倍增法

$$
\left[x^{0}\right]f^{-1}\left(x\right)=\left(\left[x^{0}\right]f\left(x\right)\right)^{-1}
$$

假设现在已经求出了 $f\left(x\right)$ 在模 $x^{\left\lceil\frac{n}{2}\right\rceil}$ 意义下的逆元 $f^{-1}_{0}\left(x\right)$。
有：

$$
\begin{aligned}
	f\left(x\right)f^{-1}_{0}\left(x\right)&\equiv 1 &\pmod{x^{\left\lceil\frac{n}{2}\right\rceil}}\\
	f\left(x\right)f^{-1}\left(x\right)&\equiv 1 &\pmod{x^{\left\lceil\frac{n}{2}\right\rceil}}\\
	f^{-1}\left(x\right)-f^{-1}_{0}\left(x\right)&\equiv 0 &\pmod{x^{\left\lceil\frac{n}{2}\right\rceil}}
\end{aligned}
$$

两边平方可得：

$$
f^{-2}\left(x\right)-2f^{-1}\left(x\right)f^{-1}_{0}\left(x\right)+f^{-2}_{0}\left(x\right)\equiv 0 \pmod{x^{n}}
$$

两边同乘 $f\left(x\right)$ 并移项可得：

$$
f^{-1}\left(x\right)\equiv f^{-1}_{0}\left(x\right)\left(2-f\left(x\right)f^{-1}_{0}\left(x\right)\right) \pmod{x^{n}}
$$

### 多项式 EXP
使用半在线卷积解决
$$
\begin{aligned}
f(x)&=\exp(g(x))\\
f'(x)&=g'(x)\exp(g(x))\\
f'(x)&=g'(x)f(x)\\
f(x)&=\int g'(x)f(x)\\
[x^n]f(x)&=\frac{1}{n}[x^{n-1}]g'(x)f(x)\\
f_n&=\frac{1}{n}\sum\limits_{k=0}^{n-1}f_{k}((n-k)g_{n-k})\\
f_n&=\frac{1}{n}\sum\limits_{k=0}^{n-1}f_{k}b_{n-k}\\
f_n&=\frac{1}{n}\sum\limits_{k=1}^nf_{n-k}b_k
\end{aligned}
$$

- 整数拆分  

$P(n)$ 为 $n$ 拆分为若干个正整数的方案数，$P(n,k)$ 为 $n$ 拆分为 $k$ 个正整数的方案数。  
$P(n)=\sum\limits_{i=1}^nP(n,i)$  
$P(n,k)=P(n-1,k-1)+P(n-k,k)$  
$Z(n,k)$ 为 $n$ 拆分为若干个自然数的方案数，$Z(n-k,k)=P(n,k)\Rightarrow Z(n,k)=Z(n-k,k)+Z(n-1,k-1)$  
生成函数 $F_k(x)=\sum\limits_{i\ge0}Z(i,k)x^i$，则有 $F_k(x)=F_{k-1}(x)+x^kF_k(x)$  
$F_k=\prod\limits_{i\ge1}^k\dfrac{1}{1-x^i}$，分母可以用分治 + FFT或者取对数算出，然后再求逆。  

这个封闭形式，也可以根据 Ferries 图的理论，将 $n$ 拆分成 $k$ 个自然数的方案数等于将 $n$ 拆分为若干个不大于 $k$ 的自然数的方案数，这是一个完全背包，可以用生成函数优化。  
填 $1$ 的生成函数: $\sum x^i=\dfrac{1}{1-x}$, 填 $2$ 的生成函数: $\sum x^{2i}=\dfrac{1}{1-x^2}$, 填 $3$ 的生成函数: $\sum x^{3i}=\dfrac{1}{1-x^3}\dots$  
