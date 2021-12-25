---
title: 高考题面背后的故事
math: true
date: 2021-12-25 21:33:25
categories: 数学
tags:
- 数学
---
做到过这样一道题
<!--more-->
![](/img/2021-12-25|21-32-07screenshot.png)
其实是2021年新高考Ⅱ卷数学 21题
好像高考考概率压轴题还是挺少见的，不过其实这更像导数题。
今天我要说的当然不是这道题的解法，而是第 $2$ 问中的已知条件的由来。

即$p$为什么是方程$p_0+p_1x+p_2x^2+p_3x^3=x$的根。
我们将该生物群体的每一个个体编号。首先如果以$0$为祖先的群体灭绝，那么相当于所有$0$的下一代为祖先的群体都灭绝。
![](/img/graph.png)
题设每一代繁殖独立且分布列相同。于是$0$为祖先的群体灭绝的概率和每个孩子们($1$,$2$,$3$)为祖先的群体灭绝概率是相等的，因为后代无穷多，一代的差别可以忽略不计。
于是我们只用分类讨论$0$的孩子个数，如以 $3$ 个孩子的情况为例：每一个后代所代表的子群体灭绝的概率都是$P$，于是$p=p_3*p^3$。
其他两种情况类似，于是结论是显然的。

本人学艺不精，语言难免不清晰，尽情谅解。