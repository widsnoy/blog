---
title: 'BZOJ 3723: [PA2014Final] Gra w podwajanie 题解'
categories: ACM
tags:
  - 模拟
abbrlink: 1eb8
date: 2024-02-01 11:08:48
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
mathjax:
aplayer:
highlight_shrink:
aside:
---

考虑倒着做，从起点开始扩展打印出可能的形状。比如从起点是 16, 向四周扩展一个格子，变为 8、8...可以发现最大填的数是 16，合法的形状不大于 9x9。  
实际上预处理可行的形状是非常麻烦的，这里提供一个~~全网最~~简单的做法，反正我没看懂 [Claris 大神的代码](https://www.cnblogs.com/clrs97/p/5288942.html) qwq（  
反正数据范围很小，我们直接用一个单调队列保存状态来搜索，判断重复可以重载一个比较结构体用 map 判重。保存形状可以用一个 __int128。
<!--more-->

```cpp
//能确定的事情只有啊，今天感觉有点寂寞啊
#include <bits/stdc++.h>
//#include <ext/pb_ds/assoc_container.hpp>
//#include <ext/pb_ds/tree_policy.hpp>
//using namespace __gnu_pbds;
using namespace std;

#define fi first
#define se second
typedef pair<int, int> pii;
typedef long long ll;
typedef long double ld;
typedef priority_queue<int> pq;
//std::mt19937_64 rng(std::chrono::steady_clock::now().time_since_epoch().count());

const int mod = 998244353;
const int N = 205;

struct cmp {
    bool operator () (const pq &a, const pq &b) const {
        if (a.size() != b.size()) return a.size() < b.size();
        pq x = a, y = b;
        while (!x.empty()) {
            if (x.top() != y.top()) return x.top() < y.top();
            x.pop(), y.pop();
        }
        return 0;
    }
};
map<priority_queue<int>, int, cmp> mp;
template <class T, class S, class C>
S& Container(priority_queue<T, S, C>& q) {
    struct HackedQueue : private priority_queue<T, S, C> {
        static S& Container(priority_queue<T, S, C>& q) {
            return q.*&HackedQueue::c;
        }
    };
    return HackedQueue::Container(q);
}

// 12~8 v 7~4 x 3~0 y
int zip(int v, int x, int y) {
    return (v << 8) + (x << 4) + y;
}
array<int, 3> unzip(int z) {
    return {z >> 8, (z >> 4) & ((1 << 4) - 1), z & ((1 << 4) - 1)};
}
int dx[4] = {1, 0, -1, 0}, dy[4] = {0, 1, 0, -1};
vector<__int128> mv[17];
__int128 bin[100];

void dfs(pq sta) {
    if (mp.count(sta)) return;
    mp[sta] = 1;
   // cout << sta.size() << '\n';
    pq q = sta;
    auto [v, x, y] = unzip(q.top());
    if (v == 1) {
        int siz = sta.size();
        __int128 stt = 0;
        while (!sta.empty()) {
            auto [v, x, y] = unzip(sta.top());
            stt |= bin[9 * (x - 1) + y];
            sta.pop();
        }
        mv[siz].push_back(stt);
        return;
    }
    q.pop();
    q.push(zip(v / 2, x, y));
    const vector<int> &vec = Container(sta);
    for (int k = 0; k < 4; k++) {
        int nx = x + dx[k], ny = y + dy[k];
        if (nx > 9 || nx < 1 || ny > 9 || ny < 1) continue;
        int ok = 1;
        for (auto i : vec) {
            auto [vv, xx, yy] = unzip(i);
            if (xx == nx && ny == yy) {ok = 0; break;}
        }
        if (!ok) continue;
        pq nxt = q;
        nxt.push(zip(v / 2, nx, ny));
        dfs(nxt);
    }
}
string s;
int a[N][N], n, m;

__int128 get(int x, int y) {
    __int128 res = 0;
    for (int i = x - 4; i <= x + 4; i++) {
        for (int j = y - 4; j <= y + 4; j++) {
            int rx = i - (x - 4) + 1, ry = j - (y - 4) + 1;
            __int128 v;
            if (i > n || i < 1 || j > m || j < 1) v = 0;
            else v = a[i][j];
            res |= bin[(rx - 1) * 9 + ry] * v;
        }
    }
    return res;
}


void MAIN() {
    bin[0] = 1;
    for (int i = 1; i < 100; i++) bin[i] = bin[i - 1] * (__int128)(2);
    pq q;
    q.push(zip(16, 5, 5));
    dfs(q);
    q.pop();
    q.push(zip(8, 5, 5));
    dfs(q);
    q.pop();
    q.push(zip(4, 5, 5));
    dfs(q);
    q.pop();
    q.push(zip(2, 5, 5));
    dfs(q);
    cin >> n >> m;
    for (int i = 1; i <= n; i++) {
        cin >> s;
        for (int j = 1; j <= m; j++) a[i][j] = s[j - 1] - '0';
    }
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++) {
            __int128 res = get(i, j);
            int mx = a[i][j];
            for (int k = 16; k >= 2; k /= 2) {
                for (__int128 x : mv[k]) if ((res & x) == x) {
                    mx = max(mx, k);
                    break;
                }
            }
            cout << mx << ' ';
        }
        cout << '\n';
    }
}

int main() {
    ios::sync_with_stdio(0), cin.tie(0);
    int T = 1;
    //cin >> T;
    while (T--) MAIN();
    return 0;
}
```