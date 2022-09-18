---
title: Old Driver Tree「珂朵莉树」
math: true
categories:
  - 模板
tags:
  - 珂朵莉树
abbrlink: '5465'
date: 2020-08-29 15:00:00
---


>感觉这东西也不算是数据结构...
模板题见[CF896C](https://codeforces.com/contest/896/problem/C)
随机数据的复杂度证明见[知乎专栏](https://zhuanlan.zhihu.com/p/102786071)  

~~虽然构造数据只能拿来骗分~~，有时候作为辅助工具是比较方便的。  
只用讲基本的操作。  
大概是把相同的元素合并成一个块以减少时间复杂度。  

## 块信息
```cpp
struct Node-t {
    int l, r;
    mutable int v;
    Node-t(const int &il, const int &ir, const int &iv) : l(il), r(ir), v(iv) {}
    inline bool operator<(const Node-t &o) const { return l < o.l; }
};
```  
mutable 的意思是“可变的”，让我们可以在后面的操作中修改 v 的值。在 C++ 中，mutable 是为了突破 const 的限制而设置的。被 mutable 修饰的变量（mutable 只能用于修饰类中的非静态数据成员），将永远处于可变的状态，即使在一个 const 函数中。

## split函数
```cpp
#define IT set<node>::iterator
IT split(int pos) {
    IT it = s.lower-bound(node(pos));
    if (it->l == pos && it != s.end())
        return it;
    --it;
    int v = it->v, l = it->l, r = it->r;
    s.erase(it);
    s.insert(node(l, pos - 1, v));
    return s.insert(node(pos, r, v)).first;
}
```
注意 set 自带的 lower-bound 和 upper-bound 的时间复杂度为$\mathjaxcal{O}(log n)$。
但使用 algorithm 库中的 lower-bound 和 upper-bound 函数对 set 中的元素进行查询，时间复杂度为 $\mathjaxcal{O}(n)$。似乎是 set 不支持随机访问的原因。  
s.insert(node(pos, r, v)).first 返回的是插入元素的地址。  

## assign函数
```cpp
void assign(int l, int r, int v) {
    IT itr = split(r + 1), itl = split(l);
    s.erase(itl, itr);
    s.insert(node(l, r, v));
}
```
作用是推平一段区间，也是$ODT$时间复杂度的保证。  
注意如果先 split 左端点再 split 右端点就会 RE，原因是 l 和 r 在一个 node 上时，split(r) 会 erase 这个 node 再重新插入，导致 split(l) 的迭代器失效。  

没被卡的习题比较少...
-  [「SHOI2015」脑洞治疗仪](https://loj.ac/problem/2037) 
-  [「Luogu 2787」理理思维](https://www.luogu.com.cn/problem/P2787) 
-  [「Luogu 4979」矿洞：坍塌](https://www.luogu.com.cn/problem/P4979)   

放一下正经的题  
- [LOJ 2005 「TJOI / HEOI2016」排序](https://loj.ac/problem/2055)
- [LOJ 2958 「COCI 2009.10」ALADIN](https://loj.ac/problem/2958)