---
title: LOJ 528「LibreOJ β Round #4」求和
mathjax: true
date: 2020-08-30 21:14:00
categories: 
  - 题解
tags: 
  - 莫比乌斯反演
  - 数论分块
---


##题目
$$\sum\limits_{i=1}^{n}\sum\limits_{j=1}^{m}\mu ^2(\gcd (i,j))\bmod 998244353$$  
##题解
以下默认$n\leq m$
$$
\begin{equation}
\begin{aligned}
&\sum\limits_{i=1}^{n}\sum\limits_{j=1}^{m}\mu ^2(\gcd (i,j))\\
&=\sum\limits_{d=1}^{n}\mu ^2(d)\sum\limits_{i=1}^{n}\sum\limits_{j=1}^{m}[\gcd (i,j)=d]\\
&=\sum\limits_{d=1}^{n}\mu ^2(d)\sum\limits_{d\mid t} \mu (\frac{t}{d})\left\lfloor\frac{m}{t}\right\rfloor \left\lfloor\frac{n}{t}\right\rfloor \\
&=\sum\limits_{t=1}^{n}\left\lfloor\frac{m}{t}\right\rfloor \left\lfloor\frac{n}{t}\right\rfloor\sum\limits_{d\mid t}\mu ^2(d)\mu (\frac{t}{d})
\end{aligned}
\end{equation}
$$

关于第二个等号,用的是莫比乌斯反演的一个结论。  
如果$f(d)=\sum\limits_{d\mid t}g(t)$  
那么$g(d)=\sum\limits_{d\mid t}\mu (\frac{t}{d})f(t)$  

这里不妨把$f(t)$看作$\sum\limits_{i=1}^{n}\sum\limits_{j=1}^{m}[t\mid \gcd (i,j)]$  
而$g(d)=\sum\limits_{i=1}^{n}\sum\limits_{j=1}^{m}[d=\gcd (i,j)]$  
不难发现$f(t)=\sum\limits_{t\mid d}g(d)$  
大家都知道$f(t)=\left\lfloor\frac{n}{t}\right\rfloor\left\lfloor\frac{m}{t}\right\rfloor$  
所以第二个等号即得证。  

交换一下求和符号，即得到了$$\sum\limits_{t=1}^{n}\left\lfloor\frac{m}{t}\right\rfloor \left\lfloor\frac{n}{t}\right\rfloor\sum\limits_{d\mid t}\mu ^2(d)\mu (\frac{t}{d})$$  

发现前面可以分块，所以只用关心怎么求$\sum\limits_{d\mid t}\mu ^2(d)\mu (\frac{t}{d})$  
令$f(t)=\sum\limits_{d\mid t}\mu ^2(d)\mu (\frac{t}{d})$  
$f$显然是积性函数，根据积性函数的性质，发现
1.$f(p)=0$  
2.$f(p^2)=-1$  
3.$f(p^k)=0$,$p\geq 3$  

其中 p 代表一个素数，只用带回定义式就可以得出。  
再结合一下莫比乌斯函数的定义， 可以得到
$$
\begin{equation}
    f(x)=
   \begin{cases}
   \mu (\sqrt{x})&\mbox{if $\sqrt{x}\in \mathjaxbb{N}$}\\
   0\,&\mbox{overwise}
   \end{cases}
\end{equation}
$$  

最后， 
$$
\begin{equation}
\begin{aligned}
&\sum\limits_{t=1}^{n}\left\lfloor\frac{m}{t}\right\rfloor \left\lfloor\frac{n}{t}\right\rfloor\sum\limits_{d\mid t}\mu ^2(d)\mu (\frac{t}{d})\\
&=\sum\limits_{t=1}^{n}\left\lfloor\frac{m}{t}\right\rfloor \left\lfloor\frac{n}{t}\right\rfloor f(t)\\
&=\sum\limits_{t=1}^{n}\left\lfloor\frac{m}{t}\right\rfloor \left\lfloor\frac{n}{t}\right\rfloor \mu (\sqrt{t})[\sqrt{t} \in \mathjaxbb{N}]\\
\end{aligned}
\end{equation}
$$
数论分块即可，时间复杂度$\mathjaxcal{O}(\sqrt{n})$

##代码
```cpp
#include <cstdio>
#include <algorithm>
#include <cmathjax>
using namespace std;
typedef long long ll;
const int maxn = 1e7;
const int mod = 998244353;
ll n, m;
bool p[maxn + 5];
int np[maxn + 5], ps;
int mu[maxn + 5];
void make-prime(int tn) {
    p[0] = p[1] = 1;
    mu[1] = 1;
    for (int i = 2; i <= tn; i++) {
        if (!p[i]) {
            mu[i] = -1;
            np[++ps] = i;
        }
        for (int j = 1; 1ll * i * np[j] <= tn; j++) {
            p[i * np[j]] = 1;
            if (i % np[j] == 0)
                break;
            mu[i * np[j]] = -mu[i];
        }
    }
    for (int i = 1; i <= tn; i++) mu[i] = (mu[i] + mu[i - 1] + mod) % mod;
}
ll solve(ll tn, ll tm) {
    ll s = 0;
    if (tn > tm)
        swap(tn, tm);
    for (ll l = 1, r, sql, sqr; l <= tn; l = r + 1) {
        r = min(tn / (tn / l), tm / (tm / l));
        sql = (int)(sqrt((l - 1) * 1.0));
        sqr = (int)(sqrt(r * 1.0));
        s = (s + (tn / l) % mod * ((tm / l) % mod) % mod * (mu[sqr] - mu[sql] + mod) % mod) % mod;
    }
    return s;
}
int main() {
    scanf("%lld %lld", &n, &m);
    make-prime((int)(sqrt(max(n, m) * 1.0)));
    printf("%lld\n", solve(n, m));
    return 0;
}
```