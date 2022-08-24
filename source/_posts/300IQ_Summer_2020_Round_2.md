---
title: 300IQ Summer 2020 Round 2
mathjax: true
categories:
  - 测试
tags:
  - CDQ分治
  - hash
  - 扩展欧几里得
abbrlink: 6d31
date: 2020-08-29 14:52:00
description:
picUrl:
---


# Problem A. Alternating Paths  
还没改啊,先鸽着吧...  
# Problem B. Dispatch Money  
假设区间$[l,r]$的逆序对数是$f_{l,r}$.  
显然$f_{l,r}=f_{l+1,r}+f_{l,r-1}-f_{l+1,r-1}+[P-l>P-r]$.  
即$f_{l,r} + f_{l+1,r-1} \geq f_{l+1,r}+f_{l,r-1}$,所以决策具有单调性.  
那么可以用想到[CDQ分治](https://oi-wiki.org/misc/cdq-divide/)来优化这个$1D$的$DP$方程.  
主要是快速算出区间的逆序对,感觉像莫队一样啊.  
还是比较套路,不过这个$\mathjaxcal{O}(nlog^3n)$真的能过?

```cpp
#include<bits/stdc++.h>
using namespace std;

typedef long long ll;
const int N = 300005;
int n, x, p[N], t[N];
vector<int> b; ll f[N];

void update(int x, int v) {for(; x <= n; x += x & -x) t[x] += v;}
int query(int x) {int res = 0; for(; x; x -= x & -x) res += t[x]; return res;}
int query(int l, int r) {return query(r) - query(l - 1);}

ll inverse(int L, int R) { //求区间逆序对,类似于莫队?
    static int l = 1, r = 0;
    //printf("%d %d\n", L, R);
    static ll res = 0;
    while(l > L) l--, res += query(1, p[l]), update(p[l], 1);
    while(r < R) r++, res += query(p[r], n), update(p[r], 1);
    while(l < L) update(p[l], -1), res -= query(1, p[l]), l++;
    while(r > R) update(p[r], -1), res -= query(p[r], n), r--;
    //cout << res << endl;
    return res;
}

void CDQ(int tl, int tr, int l, int r) { // 从[tl,tr] 转移到 [l,r]
    if(l > r) return;
    int mid = (l + r) >> 1, pos = 0; ll res = 1e17;
    for(int i = tl; i <= tr; i++) {  //枚举转移点
        ll v = inverse(i + 1, mid) + f[i] + x;
        if(v < res) res = v, pos = i;
    }
    f[mid] = min(f[mid], res); CDQ(tl, pos, l, mid - 1), CDQ(pos, tr, mid + 1, r);
}

void CDQ(int l, int r) {
    if(l >= r) return;
    int mid = (l + r) >> 1;
    CDQ(l, mid); CDQ(l, mid, mid + 1, r); CDQ(mid + 1, r);
}

int main() {
    //freopen("text.in", "r", stdin);
    memset(f, 0x3f, sizeof f);
    scanf("%d %d", &n, &x);
    for(int i = 1; i <= n; i++) scanf("%d", &p[i]), b.push-back(p[i]);
    sort(b.begin(), b.end());
    b.erase(unique(b.begin(), b.end()), b.end());
    for(int i = 1; i <= n; i++) p[i] = lower-bound(b.begin(), b.end(), p[i]) - b.begin() + 1;
    f[0] = 0; CDQ(0, n);printf("%lld\n", f[n]);
    return 0;
}
```  

# Problem C. Exerci
这个很容易想到$hash$来搞吧.  
最开始想到可能会把式子变形什么的,发现好像不太可行?  
然后又想到了两个乱搞的方法:  
1. 根据末尾的数字来判断,不过好像不太可行.
2. 用扩展欧几里得来解出一组特解,然后通解是可知并且有限的.如果可能的通解太多,就可以扔到一个序列里面,最后$\mathjaxcal{O}(n^2)$暴力考虑这些序列的元素有没有可能有解.否则直接用$hash$表来判断每对解是否合法.  

其实解法$2$确实是可行的,但感觉不太对一直没有写...   
实际上解比较少,这种方法跑得还挺快?  

话说直接$\mathjaxcal{O}(n^2)$,$6s$为什么可以跑$50000$的数据啊?  
机房的同学还一直用魔法卡常,我直接写的$for$循环反而很快过了$subtask1$?  
因为存$hash$表时要用点小技巧,还得开$int128$才行.  

```cpp
#include<bits/stdc++.h>
using namespace std;

#define int --int128
const int N = 200005;
int n, k, x[N], y[N];
vector<int> v;

int read() {
    int w = 0, f = 1; char ch = getchar();
    while(ch > '9' || ch < '0') {
        if(ch == '-') f = -1;
        ch = getchar();
    }
    while(ch >= '0' && ch <= '9') {
        w = w * 10 + ch - 48;
        ch = getchar();
    }
    return w * f;
}
void print(int x) {
    if(x < 0) {
        putchar('-');
        x = -x;
    }
    if(x > 9) print(x / 10);
    putchar(x % 10 + 48);
}
void exgcd(int a, int b, int &d, int &x, int &y) {
    if(b == 0) {x = 1, d = a, y = 0; return;}
    exgcd(b, a % b, d, y, x); y -= x * (a / b);
}

namespace H {
    const int M = 19260817;
    int hd[M] = {}, tot = 0;
    struct E{int nxt, a, b;}e[N];
    int f(int a, int b) {return (a * 1000000000 % M + b) % M;}
    void ins(int a, int b) {int t = f(a, b); e[++tot] = E{hd[t], a, b};hd[t] = tot;}
    int qry(int a, int b) {for(int i = hd[f(a, b)]; i; i = e[i].nxt) if(e[i].a == a && e[i].b == b) return i;return 0;}
}

signed main() {
    //freopen("text.in","r",stdin);
    n = read(), k = read();
    for(int i = 1; i <= n; i++) {
        x[i] = read(), y[i] = read();
        H::ins(x[i], y[i]);
    }
    for(int i = 1; i <= n; i++) {
        int a, b, d;
        exgcd(x[i], y[i], d, a, b);
        if(k % d != 0) continue;
        a *= k / d, b *= k / d;
        int xx = x[i] / d, yy = y[i] / d;
        if(a < 0) {int kk = (-a) / yy + 1; a += yy * kk; b -= xx * kk;}
        int kk = a / yy; a -= kk * yy; b += xx * kk;
        int root = b / xx;
        if(root > 92) {v.push-back(i); continue;}
        for(root += 1; root >= 0; root--) {
            //print(root);puts("");
            if(a < 0 || b < 0) continue;
            //print(a), putchar(' '),print(b);puts("");
            int p = H::qry(a, b);
            if(p != 0) {(print(i), putchar(' '), print(p));return 0;}
            a += yy, b -= xx;
        }
    }
    for(auto i : v)
        for(auto j : v) {
            if(x[i] * x[j] + y[i] * y[j] == k) {print(i),putchar(' '),print(j);return 0;}
        }
    puts("114514");
    return 0;
}
```