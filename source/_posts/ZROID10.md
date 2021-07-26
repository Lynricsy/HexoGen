---
title: 正睿OI Day10 总结
date: 2021-07-26
tags:
 - 培训总结
 - 可持久化数据结构
categories: 培训总结
description: 正睿OI Day10总结。
top_img: https://api.dujin.org/bing/1920.php
cover: https://tvax4.sinaimg.cn/large/0072Vf1pgy1foxkfzphb2j31hc0u0gvv.jpg
---
## 可持久化线段树（主席树）

维护所有历史版本的线段树

维护每一个节点的左右儿子和权值。每一次修改时对于每一个访问的点建立一个新的节点，将这个节点修改后的权值和左右儿子填入新点内（本身权值不变）。

![](https://i0.hdslb.com/bfs/album/faac099109223ed02c022e8ac8dbec839a84339a.png)

## 可持久化数组
等价于可持久化线段树

## 可持久化并查集
直接可持久化 parent 数组即可

## 可持久化平衡树

目前只有非旋 treap 支持可持久化


方式：类似主席树，对于每一个访问到的需要修改的点，新建一个点即可。 

## 可持久化Trie

原理也同主席树类似，可持久化 01 trie 与可持久化线段树等价。


## 二维数点

这是主席树的一种经典应用。

只需要对每个x建立一个版本，保存这个x之前有多少点。然后在查询时查询`rot[r]->query(x,y)-rot[l]->query(x,y)`即可。

## T1

### 题目

给定两个长度均为 $n$ 的 **排列**。
- $m$ 次询问。每次询问您要求出在第一个排列的 $[l_1,r_1]$ 和第二个排列的 $[l_2,r_2]$ 同时出现的数有多少个。
- $1\le n\le 10^6$，$1\le m\le 2\times 10^5$。强制在线。

### 思路

这个题一眼可能完全想不到什么可持久化。但是仔细一看，我们可以用每个数字的第一个数列和第二个数列出现位置组成一个坐标，然后使用刚才的二维数点即可。

### 代码

```cpp
/**************************************************************
 * Problem: OJ ProID
 * Author: 芊枫
 * Date: 2021 Mon Date
 * Algorithm:
 **************************************************************/

#include <algorithm>
#include <cstdio>
#include <cstring>
#include <iostream>
#include <map>
#include <queue>
#include <set>
#include <shared_mutex>
#include <string>

#define INF 0x3f3f3f3f3f3f3f3f
#define IINF 0x3f3f3f3f

using namespace std;

inline long long read() {
  long long x = 0;
  int f = 1;
  char ch = getchar();
  while (ch < '0' || ch > '9') {
    if (ch == '-')
      f = -1;
    ch = getchar();
  }
  while (ch >= '0' && ch <= '9') {
    x = (x << 1) + (x << 3) + (ch ^ 48);
    ch = getchar();
  }
  return x * f;
}
void write(const long long &x) {
  if (!x) {
    putchar('0');
    return;
  }
  char f[100];
  long long tmp = x;
  if (tmp < 0) {
    tmp = -tmp;
    putchar('-');
  }
  long long s = 0;
  while (tmp > 0) {
    f[s++] = tmp % 10 + '0';
    tmp /= 10;
  }
  while (s > 0) {
    putchar(f[--s]);
  }
}

const long long maxN = 100090;
long long rot[maxN];
long long cntNode;
long long totN;
long long totQ;
long long lANS;
long long pos[maxN];
long long twp[maxN];

struct Point {
  long long x, y;
} points[maxN];
struct Node {
  long long l, r;
  long long lch, rch;
  long long val;
  long long tag;
} nodes[maxN];

void insert(long long &curNode, long long x, long long L, long long R,
            long long last) {
  curNode = ++cntNode;
  nodes[curNode].lch = nodes[last].lch;
  nodes[curNode].rch = nodes[last].rch;
  nodes[curNode].val = nodes[last].val + 1;
  if (L == R) {
    return;
  }
  long long Mid = (L + R) >> 1;
  if (x <= Mid) {
    insert(nodes[curNode].rch, x, L, Mid, nodes[last].lch);
  } else {
    insert(nodes[curNode].lch, x, Mid + 1, R, nodes[last].rch);
  }
}
long long query(long long curNode, long long l, long long r, long long L,
                long long R) {
  if ((L <= l) && (R >= r)) {
    return nodes[curNode].val;
  }
  long long nowResult = 0;
  long long Mid = (l + r) >> 1;
  if (L <= Mid) {
    nowResult += query(nodes[curNode].lch, l, Mid, L, R);
  }
  if (R > Mid) {
    nowResult += query(nodes[curNode].rch, Mid + 1, r, L, R);
  }
  return nowResult;
}
long long f(long long x) { return (x - 1 + lANS) % totN + 1; }

int main() {
  totN = read();
  for (int i = 1; i <= totN; ++i) {
    pos[read()] = i;
  }
  for (int i = 1; i <= totN; ++i) {
    twp[i] = pos[read()];
  }
  for (int i = 1; i <= totN; ++i) {
    insert(rot[i], twp[i], 1, totN, rot[i - 1]);
  }
  totQ = read();
  for (int i = 1; i <= totQ; ++i) {
    long long x = f(read());
    long long y = f(read());
    long long z = f(read());
    long long w = f(read());
    if (x > y) {
      swap(x, y);
    }
    if (z > w) {
      swap(z, w);
    }
    lANS = query(rot[w], 1, totN, x, y) - query(rot[z - 1], 1, totN, x, y);
    write(lANS);
    ++lANS;
    putchar('\n');
  }
} // Thomitics Code
```

## T2

### 题目

本题的故事发生在魔力之都，在这里我们将为你介绍一些必要的设定。

魔力之都可以抽象成一个 $n$ 个节点、$m$ 条边的无向连通图（节点的编号从 $1$ 至 $n$）。我们依次用 $l,a$ 描述一条边的**长度、海拔**。

作为季风气候的代表城市，魔力之都时常有雨水相伴，因此道路积水总是不可避免的。由于整个城市的排水系统连通，因此**有积水的边一定是海拔相对最低的一些边**。我们用**水位线**来描述降雨的程度，它的意义是：所有海拔**不超过**水位线的边都是**有积水**的。

Yazid 是一名来自魔力之都的 OIer，刚参加完 ION2018 的他将踏上归程，回到他温暖的家。Yazid 的家恰好在魔力之都的 $1$ 号节点。对于接下来 $Q$ 天，每一天 Yazid 都会告诉你他的出发点 $v$ ，以及当天的水位线 $p$。

每一天，Yazid 在出发点都拥有一辆车。这辆车由于一些故障不能经过有积水的边。Yazid 可以在任意节点下车，这样接下来他就可以步行经过有积水的边。但车会被留在他下车的节点并不会再被使用。
需要特殊说明的是，第二天车会被重置，这意味着：
- 车会在新的出发点被准备好。
- Yazid 不能利用之前在某处停放的车。

Yazid 非常讨厌在雨天步行，因此他希望在完成回家这一目标的同时，最小化他**步行经过的边**的总长度。请你帮助 Yazid 进行计算。

本题的部分测试点将强制在线，具体细节请见【输入格式】和【子任务】。

**为了方便你快速理解，我们在表格中使用了一些简单易懂的表述。在此，我们对这些内容作形式化的说明：**

- 图形态：对于表格中该项为“一棵树”或“一条链”的测试点，保证 $m = n-1$。除此之外，这两类测试点分别满足如下限制：
  - 一棵树：保证输入的图是一棵树，即保证边不会构成回路。
  - 一条链：保证所有边满足 $u + 1 = v$。
- 海拔：对于表格中该项为“一种”的测试点，保证对于所有边有 $a = 1$。
- 强制在线：对于表格中该项为“是”的测试点，保证 $K = 1$；如果该项为“否”，则有 $K = 0$。
- 对于所有测试点，如果上述对应项为“不保证”，则对该项内容不作任何保证。

| $n$           | $m$           | $Q=$     | 测试点 | 形态   | 海拔   | 强制在线 |
| ------------- | ------------- | -------- | ------ | ------ | ------ | -------- |
| $\leq 1$      | $\leq 0$      | $0$      | 1      | 不保证 | 一种   | 否       |
| $\leq 6$      | $\leq 10$     | $10$     | 2      | 不保证 | 一种   | 否       |
| $\leq 50$     | $\leq 150$    | $100$    | 3      | 不保证 | 一种   | 否       |
| $\leq 100$    | $\leq 300$    | $200$    | 4      | 不保证 | 一种   | 否       |
| $\leq 1500$   | $\leq 4000$   | $2000$   | 5      | 不保证 | 一种   | 否       |
| $\leq 200000$ | $\leq 400000$ | $100000$ | 6      | 不保证 | 一种   | 否       |
| $\leq 1500$   | $=n-1$        | $2000$   | 7      | 一条链 | 不保证 | 否       |
| $\leq 1500$   | $=n-1$        | $2000$   | 8      | 一条链 | 不保证 | 否       |
| $\leq 1500$   | $=n-1$        | $2000$   | 9      | 一条链 | 不保证 | 否       |
| $\leq 200000$ | $=n-1$        | $100000$ | 10     | 一棵树 | 不保证 | 否       |
| $\leq 200000$ | $=n-1$        | $100000$ | 11     | 一棵树 | 不保证 | 是       |
| $\leq 200000$ | $\leq 400000$ | $100000$ | 12     | 不保证 | 不保证 | 否       |
| $\leq 200000$ | $\leq 400000$ | $100000$ | 13     | 不保证 | 不保证 | 否       |
| $\leq 200000$ | $\leq 400000$ | $100000$ | 14     | 不保证 | 不保证 | 否       |
| $\leq 1500$   | $\leq 4000$   | $2000$   | 15     | 不保证 | 不保证 | 是       |
| $\leq 1500$   | $\leq 4000$   | $2000$   | 16     | 不保证 | 不保证 | 是       |
| $\leq 200000$ | $\leq 400000$ | $100000$ | 17     | 不保证 | 不保证 | 是       |
| $\leq 200000$ | $\leq 400000$ | $100000$ | 18     | 不保证 | 不保证 | 是       |
| $\leq 200000$ | $\leq 400000$ | $400000$ | 19     | 不保证 | 不保证 | 是       |
| $\leq 200000$ | $\leq 400000$ | $400000$ | 20     | 不保证 | 不保证 | 是       |

### 思路

其实这个题挺显然的。

首先很明显，车虽然停在那里，但是并不会被重复使用，因为不会回到原处。我们能做的就只是把车开到离家尽量近的地方，然后走路回家。

所以我们只需要维护每个点的$dist$,然后并查集维护这个联通分量中能到达的$dist$最小的点的$dist$即可。

考虑先求出 $1$ 号点到其它每一个点的最短距离，设其为 $dist[x]$。

考虑对于每一个询问，如果保留海拔高于当前水位线的边，答案就是询问点所在的连通块内 $dist$ 最小值。

考虑当水位线逐渐降低时，可通行的边只加不减。于是考虑将所有边按照海拔从大到小的顺序依次加入，使用可持久化并查集维护连通关系和每个连通块 $dist$ 的最小值。

询问时直接二分当前访问哪个版本的并查集即可。


### 代码

```cpp
#include <algorithm>
#include <cstdio>
#include <cstring>
#include <queue>
#define UI unsigned int
#define RG register
#define I inline
#define R RG UI
#define G c = getchar()
using namespace std;
const int N = 2e5 + 9, M = 8e5 + 9, S = 2e7;
struct NODE {
  UI u, d;
  I bool operator<(RG NODE x) const { return d > x.d; }
}; 
struct EDGE {
  UI u, v, a;
  I bool operator<(RG EDGE x) const { return a < x.a; }
} e[M];
priority_queue<NODE> q;
UI n, L, p, he[N], ne[M], to[M], l[M], a[M], b[M], d[N], rt[M], lc[S], rc[S],
    f[S], mn[S], dep[S];
bool vis[N];
I UI in() {
  RG char G;
  while (c < '-')
    G;
  R x = c & 15;
  G;
  while (c > '-')
    x *= 10, x += c & 15, G;
  return x;
}
void build(R &u, R l, R r) { 
  u = ++p;
  if (l == r) {
    mn[u] = d[f[u] = l];
    return;
  }
  R m = (l + r) >> 1;
  build(lc[u], l, m);
  build(rc[u], m + 1, r);
}
UI ins(R *u, R v, R t) { 
  R l = 1, r = n, m;
  while (l != r) {
    *u = ++p;
    m = (l + r) >> 1;
    if (t <= m)
      r = m, rc[*u] = rc[v], u = lc + *u, v = lc[v];
    else
      l = m + 1, lc[*u] = lc[v], u = rc + *u, v = rc[v];
  }
  return *u = ++p;
}
UI getf(R rt, R t) {
  R u, l, r, m;
  while (1) {
    u = rt;
    l = 1;
    r = n;
    while (l != r) {
      m = (l + r) >> 1;
      if (t <= m)
        r = m, u = lc[u];
      else
        l = m + 1, u = rc[u];
    }
    if (t == f[u])
      break;
    t = f[u];
  }
  return u;
}
int main() {
  R T = in(), m, i, j, u, v, w;
  while (T--) {
    p = 0;
    n = in();
    m = in(); 
    for (i = 1; i <= m; ++i) {
      u = in();
      v = in();
      ne[++p] = he[u];
      to[he[u] = p] = v;
      ne[++p] = he[v];
      to[he[v] = p] = u;
      l[p] = l[p - 1] = in();
      e[i] = (EDGE){u, v, a[p] = a[p - 1] = in()};
    }
    memset(d, -1, (n + 1) << 2); 
    p = d[1] = 0;
    q.push((NODE){1, 0});
    while (!q.empty()) {
      RG NODE cur = q.top();
      q.pop();
      if (vis[u = cur.u])
        continue;
      vis[u] = 1;
      for (i = he[u]; i; i = ne[i])
        if (d[to[i]] > d[u] + l[i])
          q.push((NODE){to[i], d[to[i]] = d[u] + l[i]});
    }
    R q = in(), k = in(), s = in(), lans = 0;
    sort(e + 1, e + m + 1);
    for (i = 1; i <= m; ++i)
      b[i] = e[i].a;
    b[m + 1] = s + 1;
    L = unique(b + 1, b + m + 2) - b - 1;
    build(rt[L], 1, n);
    for (i = L - 1, j = m; i; --i) {
      rt[i] = rt[i + 1];
      for (; j && e[j].a == b[i]; --j) {
        if ((u = getf(rt[i], e[j].u)) == (v = getf(rt[i], e[j].v)))
          continue;
        if (dep[u] > dep[v])
          swap(u, v);
        f[ins(&rt[i], rt[i], f[u])] = f[v];
        w = ins(&rt[i], rt[i], f[v]);
        f[w] = f[v];
        mn[w] = min(mn[u], mn[v]);
        dep[w] = dep[v] + (dep[u] == dep[v]);
      }
    }
    while (q--) {
      v = (in() + k * lans - 1) % n + 1;
      u = (in() + k * lans) % (s + 1);
      printf("%u\n",
             lans = mn[getf(rt[upper_bound(b + 1, b + L + 1, u) - b], v)]);
    }
    memset(vis, 0, n + 1);
    memset(he, 0, (n + 1) << 2);
    memset(rt, 0, (L + 1) << 2);
    memset(lc, 0, (p + 1) << 2);
    memset(rc, 0, (p + 1) << 2);
    memset(f, 0, (p + 1) << 2);
    memset(mn, 0, (p + 1) << 2);
    memset(dep, 0, (p + 1) << 2);
  }
  return 0;
}
```

## T3

### 题目

小粽是一个喜欢吃粽子的好孩子。今天她在家里自己做起了粽子。

小粽面前有 $n$ 种互不相同的粽子馅儿，小粽将它们摆放为了一排，并从左至右编号为 $1$ 到 $n$。第 $i$ 种馅儿具有一个非负整数的属性值 $a_i$。每种馅儿的数量都足够多，即小粽不会因为缺少原料而做不出想要的粽子。小粽准备用这些馅儿来做出 $k$ 个粽子。

小粽的做法是：选两个整数数 $l$,  $r$，满足 $1 \leqslant l \leqslant r \leqslant n$，将编号在 $[l, r]$ 范围内的所有馅儿混合做成一个粽子，所得的粽子的美味度为这些粽子的属性值的**异或和**。（异或就是我们常说的 xor 运算，即 C/C++ 中的 `ˆ` 运算符或 Pascal 中的 `xor` 运算符）

小粽想品尝不同口味的粽子，因此它不希望用同样的馅儿的集合做出一个以上的
粽子。

小粽希望她做出的所有粽子的美味度之和最大。请你帮她求出这个值吧！

### 思路

观察到价值是 $\bigoplus_{i=l}^r a_i$ ，我们可以定义 $s_i=\bigoplus_{j=1}^i a_j$ ，那么 $val[l,r]=s_{l-1}\oplus s_r$ 。特别的，有 $s_0=0$ 。这样，我们不同的 $i,j$ 满足 $0\le i\ne j\le n$ ，对应的就是不同的区间。

题目转化为给定一个长度为 $n+1$ 的数组 $s_i$ ，求 $i<j$ 时， $s_i\oplus s_j$ 的 $\frac {n(n+1)} 2$ 种取值中前 $k$ 大的值的和。

$i<j$ 这个条件很不美观，是一个上三角形的形状，我们交换 $i,j$ ，所求值也可以表示为 $i\ne j$ 时， $s_i\oplus s_j$ 的 $n(n+1)$ 种取值中前 $2k$ 大的值的和除以 $2$ 。由于 $i=j$ 时 $s_i\oplus s_j$ 的值为 $0$ ，而 $k\le \frac {n(n-1)} 2$ ，所以我们也可以忽略 $i\ne j$ 这个条件。

我们将 $s_i$ 的 $n+1$ 个值插入 01 Trie ，给定一个 $k$ ，我们可以在 $O(log_2 a)$ 的时间复杂度内找到与 $a$ 异或结果第 $k$ 大的值。方法类似于在线段树上二分。

我们维护一个大根堆，堆中初始时加入每个 $s_i$ 与 $s$ 中元素异或的最大值。每次我们取出堆顶，累加进答案，如果我们取出的是 $s_i$ 与 $s$ 中元素异或的第 $t$ 大值，就在堆中插入 $s_i$ 与 $s$ 中元素异或的第 $t+1$ 大值。这样进行 $k$ 次操作，取出的依次就是 $s$ 中元素两两异或的前 $k$ 大值。

令 $a=\max \{a_i\}$，则时间复杂度为 $O((n+k)\log_2 a\log_2 n)$ 。

### 代码

```cpp
#include <algorithm>
#include <cstdio>
#include <cstring>
#include <queue>
using namespace std;
const int maxn = 500010;
typedef long long ll;
int n, k, cnt = 1;
ll a[maxn], ans;
struct Trie {
  int ch[2], cnt;
} st[maxn * 34];
void insert(ll v) {
  int nww = 1;
  st[nww].cnt++;
  for (ll i = 33; i >= 0; i--) {
    ll t = (v >> i) & 1ll;
    if (!st[nww].ch[t])
      st[nww].ch[t] = ++cnt;
    nww = st[nww].ch[t];
    st[nww].cnt++;
  }
}
ll query(ll v, int k) {
  int nww = 1;
  ll ans = 0;
  for (ll i = 33; i >= 0; i--) {
    ll t = (v >> i) & 1ll;
    if (st[st[nww].ch[t]].cnt >= k)
      nww = st[nww].ch[t];
    else
      k -= st[st[nww].ch[t]].cnt, nww = st[nww].ch[t ^ 1], ans |= (1ll << i);
  }
  return ans;
}
struct node {
  int id, rnk;
  ll v;
  bool operator<(node x) const { return v < x.v; }
};
priority_queue<node> q;
int main() {
  scanf("%d%d", &n, &k);
  k *= 2;
  insert(0);
  for (int i = 1; i <= n; i++)
    scanf("%lld", a + i), a[i] ^= a[i - 1], insert(a[i]);
  for (int i = 0; i <= n; i++)
    q.push({i, n + 1, query(a[i], n + 1)});
  while (k--) {
    node x = q.top();
    q.pop();
    ans += x.v;
    if (x.rnk)
      q.push({x.id, x.rnk - 1, query(a[x.id], x.rnk - 1)});
  }
  printf("%lld\n", ans / 2ll);
  return 0;
}
```

## T4

### 题目

小 R 热衷于做黑暗料理，尤其是混合果汁。

商店里有 $n$ 种果汁，编号为 $0,1,\cdots,n-1$ 。$i$ 号果汁的美味度是 $d_i$，每升价格为 $p_i$。小 R 在制作混合果汁时，还有一些特殊的规定，即在一瓶混合果汁中，$i$ 号果汁最多只能添加 $l_i$ 升。

现在有 $m$ 个小朋友过来找小 R 要混合果汁喝，他们都希望小 R 用商店里的果汁制作成一瓶混合果汁。其中，第 $j$ 个小朋友希望他得到的混合果汁总价格不大于 $g_j$，体积不小于 $L_j$。在上述这些限制条件下，小朋友们还希望混合果汁的美味度尽可能地高，一瓶混合果汁的美味度等于所有参与混合的果汁的美味度的最小值。请你计算每个小朋友能喝到的最美味的混合果汁的美味度。

### 思路

考虑二分答案。

假设二分出的果汁最小美味值是 $m$，则我们只要考虑美味值 $d_i\geq m$ 的果汁即可。所以我们想到把果汁按照 $d_i$ 排序，这样每次就不用一个一个枚举。

接下来我们就要想怎么 $\mathrm{check}$ 就好了。贪心的想，我们就按照价格 $p_i$ 从小往大取，肯定是最优的。

所以关键问题是价格，再看数据范围，只够我们再加一个 $\mathrm{logn}$，那我们就能想到根据价格建立权值线段树，又因为我们要查询的都是区间，把它优化成主席树，就会方便许多。

现在，我们想如何按照我们的贪心思路在主席树上查询。假设我们现在在节点 $o$ 上，我们肯定优先先往左子树走，因为左子树对应的价格更小嘛。但我们还要保证果汁数量要 $\geq L$。所以，这个问题就有点类似于区间第 $k$ 小的问题。如果左子树中的果汁数量 $\gep L$，我们就去左子树找；反之，我们要取完左子树的所有果汁，并把剩下的果汁带到右子树中找就行了。

因此，我们在主席树中就要存两个信息。一是对应区间的果汁数量，而是对应区间果汁全取的价格。

最后，贪心取出的价格如果 $\leq g$，并且果汁数量 $\geq L$，就是合法的。

### 代码

```cpp
#include <bits/stdc++.h>
using namespace std;

#define int long long
#define mid (l + r >> 1)

const int N = 1e5;

int n, q, ans;
struct juice {
  int d, p, m;
  bool operator<(const juice b) const { return d > b.d; }
} a[100005];
int cnt, root[100005];
struct hjtree {
  int l, r, s, sum;
} t[100005 << 6];

void insert(int &o, int pre, int l, int r, int x, int v) {
  o = ++cnt;
  t[o] = t[pre];
  t[o].s += v, t[o].sum += x * v;
  if (l == x && r == x)
    return;
  if (x <= mid)
    insert(t[o].l, t[pre].l, l, mid, x, v);
  else
    insert(t[o].r, t[pre].r, mid + 1, r, x, v);
}

int query(int o, int l, int r, int k) {
  if (l == r)
    return l * k;
  if (t[t[o].l].s >= k)
    return query(t[o].l, l, mid, k);
  else
    return t[t[o].l].sum + query(t[o].r, mid + 1, r, k - t[t[o].l].s);
}

signed main() {
  cin >> n >> q;
  for (int i = 1; i <= n; cin >> a[i].d >> a[i].p >> a[i].m, i++)
    ;
  sort(a + 1, a + 1 + n);
  for (int i = 1; i <= n; i++)
    insert(root[i], root[i - 1], 1, N, a[i].p, a[i].m);
  while (q--) {
    int g, lim;
    cin >> g >> lim;
    ans = -1;
    int l = 1, r = n;
    while (l <= r) {
      int res = query(root[mid], 1, N, lim);
      if (res <= g && t[root[mid]].s >= lim)
        ans = mid, r = mid - 1;
      else
        l = mid + 1;
    }
    cout << (ans == -1 ? -1 : a[ans].d) << endl;
  }
}
```

## T5

### 题目

印度尼西亚有 $N$ 个城市以及 $M$ 条双向道路，城市从 $0$ 到 $N - 1$ 编号，道路从 $0$ 到 $M - 1$ 编号。每条道路连接着两个不同的城市，第 $i$ 条道路连接第 $U[i]$ 个城市与第 $V[i]$ 个城市，汽车行驶这条道路将耗费 $W[i]$ 个单位汽油。通过这些道路，任意两个城
市间能够互相到达。

接下来的 $Q$ 天中, 每天会有一对城市希望建立政治关系。具体来说，第 $j$ 天，第 $X[j]$ 个城市想要和第 $Y[j]$ 个城市建立政治关系。为此，第 $X[j]$ 个城市将会派一名代表坐汽车前往第 $Y[j]$ 个城市。同样地，第 $Y[j]$ 个城市也会派一名代表坐汽车前往第 $X[j]$ 个城市。

为了避免拥塞，两辆车不应在任何时间点碰面。更具体地，两辆车不能在同一个时间点出现在同一个城市。同样地，两辆车也不应该沿相反的方向同时行驶过同一条道路。另外，汽车行驶过一条道路时必须完整经过道路并到达道路另一端的城市（换句话说，汽车不允许在道路中间掉转方向）。但是，汽车可以多次到达一个城市或是多次经过一条道路。此外，汽车可以在任何时间在任何城市等候。

由于高燃料容量汽车的价格昂贵，两个城市都分别希望选择一条路线，使得两辆汽车所需的最大单位汽油容量最小。每个城市中都有加油站并且供油量是无限的，因此汽车所需的单位汽油容量实际上就是行驶过的道路中最大的单位汽油消耗量。

你必须实现 `init` 和 `getMinimumFuelCapacity` 函数。

- `init(N, M, U, V, W)` - 该函数将在所有 `getMinimumFuelCapacity` 的调用前被评测库恰好调用一次。
	- $N$： 一个整数表示城市数。
	- $M$： 一个整数表示道路数。
	- $U$： 一个长为 $M$ 的整数序列表示道路的第一个端点城市。
	- $V$： 一个长为 $M$ 的整数序列表示道路的第二个端点城市。
	- $W$： 一个长为 $M$ 的整数序列表示道路的汽油消耗。

- `getMinimumFuelCapacity(X, Y)` - 该函数将被评测库调用恰好 $Q$ 次。
	- $X$： 一个整数表示第一个城市。
	- $Y$： 一个整数表示第二个城市。
	- 该函数必须返回一个整数，表示根据题目描述中的规则，两辆分别从第 $X$ 个城市与第 $Y$ 个城市出发要到达彼此城市的车，它们的单位汽油容量最大值的最小值。若无法满足题目规则则返回 $−1$。</div>

### 思路

对于两点 $x,y$，$x,y$ 的最小瓶颈边权（也就是最大边最小的路的最大边权）等于 $x,y$ 的最近公共祖先（LCA）的点权。

对于本题来说，也可以理解为是查询两点间的瓶颈边，我们尝试运用类似 Kruskal 重构树的思想。

首先分析原题中的描述，可以知道：两点 $x,y$ 能互相派出使者访问，当且仅当两点连通，且它们所在的连通块不是一条链。

证明较为简单，连通性显然必须满足，而如果连通块是链也显然无解，尝试一下发现只要有度数 $\geq 3$ 的点或者环那么必定有解。

将边从小到大排序，运行和 Kruskal 算法类似的过程。不同的是，我们不仅要维护连通性，还要维护一个连通块究竟是不是链。发现一条链的性质很大程度上取决于两个端点，于是不妨在并查集的根节点处维护两个数组 $st_i,en_i$，如果该连通块是链，则存储链的端点。当一个连通块从链变成非链时，我们就在 Kruskal 重构树上新建一个节点，这个节点向所有链上的点连边；连通块合并同理。不难发现这样做仍然能保证原来 Kruskal 重构树的大部分性质，而且也能用 LCA 来处理在线询问。

简单来说，我们扫描每条边：

如果边两端已经连通：如果这个连通块是一条链，那么加上这条边显然不再是链了，那么打上标记，同时在 Kruskal 重构树上连边；否则没有变化。</li>
如果没有连通：

如果原来两个连通块有至少一个不是链，那么新连通块也一定不是链；</li>
如果原来两个连通块都是链，那么新连通块是不是链取决于连边是不是在端点之间进行的。也可简单维护。</li>

注意我们由于要维护一个点所在的连通块，且要支持合并，应采用启发式合并，使时间复杂度稳定在 $O(n\log n)$。

总时间复杂度 $O((n+q)\log n)$。


### 代码

```cpp
#include"swap.h"

#include<cstdio>
#include<vector>
#include<algorithm>

using namespace std;

struct BCJ
{
    int fa[500000];
    void init(int n){for(int i=0;i<n;i++)fa[i]=i;}
    int fnd(int x){return x==fa[x]?x:fa[x]=fnd(fa[x]);}
}B;

struct e
{
    int u,v,w;e(int uu=0,int vv=0,int ww=0):u(uu),v(vv),w(ww){}
};vector<e> ed;
bool operator<(e a,e b){return a.w<b.w;}

bool conn[500000];int st[500000],en[500000],rt[500000];vector<int> pnt[500000];

vector<int> tr[500000];

int fa[500000][20],dep[500000];

void dfs(int u,int f)
{
    fa[u][0]=f;for(int i=1;i<20;i++)fa[u][i]=fa[u][i-1]==-1?-1:fa[fa[u][i-1]][i-1];
    rt[u]=f==-1?u:rt[f],dep[u]=f==-1?1:dep[f]+1;
    for(int i=0;i<tr[u].size();i++)dfs(tr[u][i],u);
}

int LCA(int x,int y)
{
    if(rt[x]!=rt[y])return -1;
    if(dep[x]<dep[y])swap(x,y);
    for(int i=19;i>=0;i--)
    {
        if(fa[x][i]!=-1&&dep[fa[x][i]]>=dep[y])x=fa[x][i];
    }
    if(x==y)return x;
    for(int i=19;i>=0;i--)
    {
        if(fa[x][i]!=fa[y][i])x=fa[x][i],y=fa[y][i];
    }
    return fa[x][0];
}
int n,m;
void init(int N, int M,vector<int> U,vector<int> V,vector<int> W)
{
    n=N,m=M;
    for(int i=0;i<M;i++)ed.push_back(e(U[i],V[i],W[i]));sort(ed.begin(),ed.end());
    B.init(N);for(int i=0;i<N;i++)st[i]=en[i]=rt[i]=i,pnt[i].push_back(i);
    for(int i=0;i<M;i++)
    {
        int u=ed[i].u,v=ed[i].v,fu=B.fnd(u),fv=B.fnd(v);
        if(pnt[fu].size()<pnt[fv].size()){swap(u,v),swap(fu,fv);}
        if(fu==fv)
        {
            if(!conn[fu])
            {
                conn[fu]=1;
                for(int j=0;j<pnt[fu].size();j++)tr[i+N].push_back(pnt[fu][j]);
                rt[fu]=i+N;
            }
        }
        else
        {
            if(conn[fu]||conn[fv])
            {
                if(conn[fu])tr[i+N].push_back(rt[fu]);
                else for(int j=0;j<pnt[fu].size();j++)tr[i+N].push_back(pnt[fu][j]);
                if(conn[fv])tr[i+N].push_back(rt[fv]);
                else for(int j=0;j<pnt[fv].size();j++)tr[i+N].push_back(pnt[fv][j]);
                conn[fu]=1,rt[fu]=i+N;B.fa[fv]=fu;
            }
            else
            {
                if((u==st[fu]||u==en[fu])&&(v==st[fv]||v==en[fv]))
                {
                    st[fu]=u^st[fu]^en[fu];en[fu]=v^st[fv]^en[fv];
                    for(int j=0;j<pnt[fv].size();j++)pnt[fu].push_back(pnt[fv][j]);
                    B.fa[fv]=fu;
                }
                else
                {
                    conn[fu]=1;
                    for(int j=0;j<pnt[fu].size();j++)tr[i+N].push_back(pnt[fu][j]);
                    for(int j=0;j<pnt[fv].size();j++)tr[i+N].push_back(pnt[fv][j]);
                    B.fa[fv]=fu;rt[fu]=i+N;
                }
            }

        }
    }
    for(int i=N+M-1;i>=0;i--)
    {
        if(!dep[i])dfs(i,-1);
    }
}

int getMinimumFuelCapacity(int X,int Y)
{

    int u=LCA(X,Y);if(u==-1)return -1;
    return ed[u-n].w;
}
```

## T6

### 题目

There is a tree with $N$ nodes, whose root is 1.<br><br>    There are $Q$ queries for you,for each query,we will give $K$ numbers,which are $A_1,A_2,\dots,A_K$.<br><br>    For every node $x\in[1,N]$ in the tree,we assume it's good if there is not a node $y\in A$,such that $y$ is the ancient of $x$ or $y=x$<br><br>    And we will give two numbers $T , P$ to show the property of each query.<br><br>    1.when $T=1$,you should output the sum of the distance between every good node and $P$.<br><br>    2.when $T=2$,you should output the minimum distance between every good node and $P$.<br><br>    3.when $T=3$,you should output the maximum distance between every good node and $P$.<br><br>    Let the distance between nodes in the tree be the shortest path between these two nodes.And we assume the length of each edge is 1.<br><br>    Specially,the distance between two same nodes is 0.<br><br>    For each query,if there is no nodes that is good,just output -1.</div><div class=panel_bottom>&nbsp;</div><br><div class=panel_title align=left>Input</div> <div class=panel_content>There are several test cases.<br><br>    For each test case,the first line contains two numbers $N$ and $Q$.<br><br>    For the following $N-1$ lines,each line contains two numbers $u$ and $v$,indicating there is a edge between $u$ and $v$ in tree.<br><br>    For the following $Q$ lines,each line contains some numbers,which are $K,P',T,A_1,A_2,\dots,A_K$ in order.<br><br>    Let the answer of last query be $lastans$,then $P=(P' + lastAns)~\mod{N} + 1$.<br><br>    If the answer of last query is -1 or it's the first query,then $lastans=0$.<br><br>    Let the number of test cases be $T$,we guarantee $T=30$.<br><br>    $80\%$ test cases satisfy $N,K,Q \le 10000$ ,$\sum{K} \le 20000$.<br><br>    $100\%$ test cases satisfy ~$P, N,K \le 50000$,$Q \le 100000$,$\sum{K} \le 200000$.</div><div class=panel_bottom>&nbsp;</div><br><div class=panel_title align=left>Output</div> <div class=panel_content>You should output Q lines in total,each line contains a number indicating the answer of each query.

### 思路

首先考虑 good node 在 dfs 序上一定最多是 k+1 个连续的区间。

然后考虑对于每一个 P，用维护出它与其它的点的距离的最大值/最小值/和。考虑当 P 变为其的某一个儿子 to 时，$[beg[to],en[to]]$ 的距离会减 1，$[1,beg[to]-1],[en[to]+1,n]$ 的距离会加 1。那么根为 to 的线段树是由根为 P 的线段树修改几下得到的。于是用主席树维护即可。

时间复杂度 $O((n+\sum k)logn)$。

## T6

### 题目

你需要动态维护一个多种括号组成的括号序列。需要支持两种操作：

- $1$ $i$ $t$：修改 $i$ 位置的括号为 $t$（绝对值表示种类，正数表示是左括号，负数表示是右括号）。
- $2$ $l$ $r$：查询 $[l, r]$ 区间是否是一个合法的括号序列。

序列长度为 $n$，不同的括号种类数为 $k$，操作次数为 $q$。

一个多种括号组成的括号序列 $S$ 是合法的当且仅当：

1. $S$ 为空。
2. $S$ 可以表示为 $A + B$ 的形式，其中 $A, B$ 都是合法的。
3. $S$ 可以表示为 $\texttt{(}^\ast + A + \texttt{)}^\ast$ 的形式，其中 $A$ 是合法的并且 $\texttt{(}^\ast$ 与 $\texttt{)}^\ast$ 是一对**相同种类的左右括号**。

例如 `()[{}()]` 和 `{([{}])}` 是合法的，而 `][][` 和 `([{)]}` 不是。

### 思路

考虑对于一个区间，如果它不能够不断地通过消除相邻的x和-x的操作，变成一个前面都是正数，后面都是负数的序列，那么我们称这个区间是不好的。

如果一个区间是不好的，那么它跟任意一个区间拼起来，一定也是不好的，证明可以分类讨论。

于是我们可以不用考虑不好的区间，发现区间是可以合并的，于是考虑用线段树维护。考虑判断能不能匹配的时候要哈希一下，还需要合并两个序列，和分离序列。于是考虑每个节点用可持久化平衡树维护两个括号序列即可。

时间复杂度 $O(nlog^2n)$。

