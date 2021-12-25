---
title: 拉格朗日插值入门
date: 2021-11-04 14:36:24
categories: 数学
math: true
tags:
- 数学
- 多项式
---
其实是多年前写的一篇笔记了，做测试文章。

<!--more-->

## 拉格朗日插值
如果给一个度数为$n$的多项式的$n+1$个点$(x_i,y_i)$，那么我们可以用拉格朗日插值将求出这个多项式在所有点的取值。  

假设$x_i$互不相同，$F(k)=\sum\limits_{i=0}^{deg} y_i \ell_i(k)$  

其中$\ell_i$是拉格朗日基本多项式，$\ell_i(k)=\prod\limits_{j=0,i\neq j}^{deg}\frac{k-x_j}{x_i-x_j}$  

这个$\ell$有什么特点呢？  
对于$\forall j\neq i$，$\ell_i(x_j)=0$，而$\ell_i(x_i)=1$。  
代值进去即可验证。  

所以$\forall x_i,F(x_i)=\sum\limits_{i=0}^{deg} y_i \ell_i(x_i)=y_i$，一共有$deg+1$个点，所以能确定一个多项式。  
时间复杂度$\mathcal{O}(n^2)$

## 重心拉格朗日插值  

每一次都算$\ell$好像很慢。  
令$\ell(x)=(x-x_1)(x-x_2)...(x-x_n)$  
$w_i=\frac{1}{\prod\limits_{j=0,i\neq j}^{deg}(x_i-x_j)}$  

则$\ell_i(x)=\frac{\ell(x)}{x-x_i}w_i$  
$F(x)=\sum\limits_{i=0}^{deg}y_i\ell_i(x)$  
$F(x)=\ell(x)\sum\limits_{i=0}^{deg}y_i\frac{w_i}{x-x_i}$  

$w_i$称为$i$的重心，每次加入新点时可以$\mathcal{O}
(n)$算新重心，计算$F$也是$\mathcal{O}(n)$的。  

~~当然初始化重心是$\mathcal{O}(n^2)$~~  

