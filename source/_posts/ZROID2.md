---
title: 正睿OI Day2 总结
date: 2021-07-18
tags:
 - 培训总结
 - 线段树
 - 树状数组
categories: 培训总结
description: 正睿OI Day2总结。
top_img: https://api.dujin.org/bing/1920.php
cover: https://tvax4.sinaimg.cn/large/0072Vf1pgy1foxkiijgosj31hc0u0140.jpg
---

## 线段树

这个就不多讲了，写过无数次了。

## 树状数组

### 功能

树状数组支持：单点修改，前缀求和

![](https://i0.hdslb.com/bfs/album/b46e3091cf2683ac1d528f34972fb775dd5d90e1.png)

![](https://i0.hdslb.com/bfs/album/f0f8342668d7f1ca1a071992c8318f9c3eb0569a.png)

### 怎么写？

#### 单点修改：
```cpp
void add(long long x,long long w) {
  for (long long tx = x; tx <= totN; tx += lowbit(tx)) {
    nodes[tx][ty] += w;
  }
}
```

#### 前缀查询：

```cpp
long long Nquery(long long x) {
  if (!x) {
    return false;
  }
  for (long long tx = x; tx; tx -= lowbit(tx)) {
    ans += nodes[tx][ty];
  }
  return ans;
}
```

#### 区间查询：

```cpp
void Rquery(long long x,long long y){
  return Nquery(y)-Nquery(x);
}
```

### 解释

一般感觉树状数组是一个很玄学的东西。那么为甚么要这样做呢？

其实树状数组每个节点维护的是一部分区间和。是从$x$到$x-lowbit(x)$的区间和（不包括$x-lowbit(x)$）。

这样说了就很好理解了。求前缀和时其实就是将前缀和分成一段一段的$lowbit$，然后加到一起就行了。

## 二维树状数组

### 优点

二维树状数组比二维线段树好写了不是一点半点。

### 思路

只需加一层循环即可。

### 怎么写？

#### 前缀查询
```cpp
long long Nquery(long long x, long long y) {
  if (!x || !y) {
    return false;
  }
  long long ans = 0;
  long long tx = x;
  long long ty = y;
  for (; tx; tx -= lowbit(tx)) {
    ty = y;
    for (; ty; ty -= lowbit(ty)) {
      ans += nodes[tx][ty];
    }
  }
  return ans;
}
```

#### 单点修改

```cpp
void add(long long x, long long y, long long w) {
  long long tx = x;
  long long ty = y;
  for (; tx <= totN; tx += lowbit(tx)) {
    ty = y;
    for (; ty <= totN; ty += lowbit(ty)) {
      nodes[tx][ty] += w;
    }
  }
}
```

好了，以上是今天的知识点。

## 练习1

### 题目

给定一个长度为 n 的排列 p，求将其冒泡排序所需要交换的次数。

### 思路

考虑答案一定是满足 $1 \leq i, j \leq n, i < j, p[i] > p[j]$ 的 $i,j$ 对数。(为什么？)

于是考虑对于每一个位置，求出前面有多少比它大的数，最后全加起来即可。

考虑从左往右扫，维护一个数集 $S$，每当遇到一个数时，询问 $S$ 中比当前数小的数(比当前数大的数的个数可以由总数- 比当前数小的数得到)，并把它加进去。

维护 $a[i]$ 表示 $S$ 中是否有值为 $i$ 的数，于是询问就是一个前缀和。用 $\text{BIT}$ 维护 $a$ 数组即可。

## 练习2

### 题目

前缀和(prefix sum)$S_i=\sum_{k=1}^i a_k$。

前前缀和(preprefix sum) 则把$S_i$作为原序列再进行前缀和。记再次求得前缀和第$i$个是$SS_i$

给一个长度n的序列$a_1, a_2, \cdots, a_n$，有两种操作：

1. `Modify i x`：把$a_i$改成$x$；
2. `Query i`：查询$SS_i$

### 思路

考虑维护前缀和数组。修改一个值相当于对前缀和数组进行后缀加。求 $\text{preprefix sum}$ 相当于求这个数组的前缀和。用线段树维护即可。

### 代码

```cpp
/**************************************************************
 * Problem: Luogu P4868
 * Author: 芊枫
 * Date: 2021 July 18
 * Algorithm: 线段树
 **************************************************************/
#include <bits/stdc++.h>
#include <csetjmp>
#include <cstdio>
#include <iostream>
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
long long totN;
long long totM;
long long nums[maxN];
long long sum[maxN];

struct Node {
  long long l, r;
  Node *lch, *rch;
  long long val, tag;
  void pushup() { val = lch->val + rch->val; }
  Node(long long L, long long R) {
    l = L;
    r = R;
    tag = 0;
    if (l == r) {
      val = sum[l];
      lch = rch = NULL;
      return;
    }
    long long Mid = (L + R) >> 1;
    lch = new Node(L, Mid);
    rch = new Node(Mid + 1, R);
    pushup();
  }
  void maketag(long long w) {
    val += (r - l + 1) * w;
    tag += w;
  }
  void pushdown() {
    if (!tag) {
      return;
    }
    lch->maketag(tag);
    rch->maketag(tag);
    tag = 0;
  }
  inline bool INrange(long long L, long long R) { return (L <= l) && (r <= R); }
  inline bool OUTrange(long long L, long long R) { return (L > r) || (l > R); }
  void update(long long L, long long R, long long w) {
    if (INrange(L, R)) {
      maketag(w);
    } else if (!OUTrange(L, R)) {
      pushdown();
      lch->update(L, R, w);
      rch->update(L, R, w);
      pushup();
    }
  }
  long long query(long long L, long long R) {
    if (INrange(L, R)) {
      return val;
    } else if (OUTrange(L, R)) {
      return 0;
    }
    pushdown();
    return lch->query(L, R) + rch->query(L, R);
  }
};

string quer;

int main() {
  totN = read();
  totM = read();
  for (int i = 1; i <= totN; ++i) {
    nums[i] = read();
    sum[i] = sum[i - 1] + nums[i];
  }
  Node *rot = new Node(1, totN);
  for (int i = 1; i <= totM; ++i) {
    cin >> quer;
    long long x, y;
    if (quer == "Query") {
      x = read();
      write(rot->query(1, x));
      putchar('\n');
    } else {
      x = read();
      y = read();
      rot->update(x, totN, y - nums[x]);
      nums[x] = y;
    }
  }
  return 0;
} // Thomitics Code
```

## 练习3

Given an N*N matrix A, whose elements are either 0 or 1. A[i, j] means the number in the i-th row and j-th column. Initially we have A[i, j] = 0 (1 <= i, j <= N).
We can change the matrix in the following way. Given a rectangle whose upper-left corner is (x1, y1) and lower-right corner is (x2, y2), we change all the elements in the rectangle by using "not" operation (if it is a '0' then change it into '1' otherwise change it into '0'). To maintain the information of the matrix, you are asked to write a program to receive and execute two kinds of instructions.

1.	C x1 y1 x2 y2 (1 <= x1 <= x2 <= n, 1 <= y1 <= y2 <= n) changes the matrix by using the rectangle whose upper-left corner is (x1, y1) and lower-right corner is (x2, y2).

2.	Q x y (1 <= x, y <= n) querys A[x, y].

### 思路

这道题说要对于一个区间取反。其实就相当于是异或1。

我们今天主要学习的树状数组，所以考虑怎样将这种区间修改转换为单点修改。

其实差分即可。

然后对于每一次询问，查询前缀疑惑和即可。

用二维树状数组维护。

### 代码

```cpp
/**************************************************************
 * Problem: POJ 2155
 * Author: 芊枫
 * Date: 2021 July 18
 * Algorithm: 二维树状数组&差分
 **************************************************************/
#include <cstdio>
#include <cstring>
#include <iostream>
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

const long long maxN = 1090;
long long totX;
long long totN;
long long totT;
long long nums[maxN];
long long nodes[maxN][maxN];

inline long long lowbit(long long x) { return x & (-x); }
void add(long long x, long long y) {
  long long tx = x;
  long long ty = y;
  for (; tx <= totN; tx += lowbit(tx)) {
    ty = y;
    for (; ty <= totN; ty += lowbit(ty)) {
      nodes[tx][ty] ^= 1;
    }
  }
}
// void build() {
//   for (int i = 1; i <= totN; ++i) {
//     add(i, nums[i]);
//   }
// }
long long Nquery(long long x, long long y) {
  if (!x || !y) {
    return false;
  }
  long long ans = 0;
  long long tx = x;
  long long ty = y;
  for (; tx; tx -= lowbit(tx)) {
    ty = y;
    for (; ty; ty -= lowbit(ty)) {
      ans ^= nodes[tx][ty];
    }
  }
  return ans;
}
// long long Rquery(long long xl, long long xr, long long yl, long long yr) {
//   return Nquery(xr, yr) ^ Nquery(xl, yl);
// }
string quer;

int main() {
  totX = read();
  while (totX--) {
    memset(nodes, 0, sizeof(nodes));
    totN = read();
    totT = read();
    for (int i = 1; i <= totT; ++i) {
      cin >> quer;
      if (quer[0] == 'Q') {
        long long x, y;
        x = read();
        y = read();
        write(Nquery(x, y));
        putchar('\n');
      } else if (quer[0] == 'C') {
        long long xl, xr, yl, yr;
        xl = read();
        yl = read();
        xr = read();
        yr = read();
        add(xl, yl);
        add(xr + 1, yr + 1);
        add(xl, yr + 1);
        add(xr + 1, yl);
      }
    }
    putchar('\n');
  }
  return 0;
} // Thomitics Code
```

## 练习4

### 题目

$hater$扔给你一个长度为$n$序列，定义其中一个连续子序列$t$的代价为：
$$
cost(t)=\sum_{x\in set(t)}last(x)−first(x)
$$
其中$set(t)$表示该子序列的元素集合，$last(x)$表示$x$在该子序列中最后一次出现的位置，$first(x)$表示$x$在该子序列中第一次出现的位置。

也就是说一个连续子序列的贡献为对于其中每种元素最后一次出现的位置与第一次出现的位置的下标差的和。

现在你要把原序列划分成$k$个连续子序列，求最小代价和。

其中$1\leq n\leq35000,1\leq k\leq min(n,100),1\leq a_i \leq n$。

### 思路

<h4>题意</h4>
<ul>
<li>一个数组的代价为：对于所有在数组里的数 <span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>x</mi></mrow><annotation encoding="application/x-tex">x</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:0.43056em;vertical-align:0em;"></span><span class="mord mathnormal">x</span></span></span></span></span>，数组中最后一个值为 <span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>x</mi></mrow><annotation encoding="application/x-tex">x</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:0.43056em;vertical-align:0em;"></span><span class="mord mathnormal">x</span></span></span></span></span> 的索引和第一个值为 <span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>x</mi></mrow><annotation encoding="application/x-tex">x</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:0.43056em;vertical-align:0em;"></span><span class="mord mathnormal">x</span></span></span></span></span> 的索引的绝对差之和；</li>
<li>给出一个长度为 <span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>n</mi></mrow><annotation encoding="application/x-tex">n</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:0.43056em;vertical-align:0em;"></span><span class="mord mathnormal">n</span></span></span></span></span> 的数组 <span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>a</mi></mrow><annotation encoding="application/x-tex">a</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:0.43056em;vertical-align:0em;"></span><span class="mord mathnormal">a</span></span></span></span></span>，分成 <span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>k</mi></mrow><annotation encoding="application/x-tex">k</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:0.69444em;vertical-align:0em;"></span><span class="mord mathnormal" style="margin-right:0.03148em;">k</span></span></span></span></span> 个连续段，求最小代价和；</li>
<li><span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mn>1</mn><mo>≤</mo><msub><mi>a</mi><mi>i</mi></msub><mo>≤</mo><mi>n</mi><mo>≤</mo><mn>35000</mn></mrow><annotation encoding="application/x-tex">1\le a_i\le n\le35000</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:0.78041em;vertical-align:-0.13597em;"></span><span class="mord">1</span><span class="mspace" style="margin-right:0.2777777777777778em;"></span><span class="mrel">≤</span><span class="mspace" style="margin-right:0.2777777777777778em;"></span></span><span class="base"><span class="strut" style="height:0.7859700000000001em;vertical-align:-0.15em;"></span><span class="mord"><span class="mord mathnormal">a</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:0.31166399999999994em;"><span style="top:-2.5500000000000003em;margin-left:0em;margin-right:0.05em;"><span class="pstrut" style="height:2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mathnormal mtight">i</span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.15em;"><span></span></span></span></span></span></span><span class="mspace" style="margin-right:0.2777777777777778em;"></span><span class="mrel">≤</span><span class="mspace" style="margin-right:0.2777777777777778em;"></span></span><span class="base"><span class="strut" style="height:0.7719400000000001em;vertical-align:-0.13597em;"></span><span class="mord mathnormal">n</span><span class="mspace" style="margin-right:0.2777777777777778em;"></span><span class="mrel">≤</span><span class="mspace" style="margin-right:0.2777777777777778em;"></span></span><span class="base"><span class="strut" style="height:0.64444em;vertical-align:0em;"></span><span class="mord">3</span><span class="mord">5</span><span class="mord">0</span><span class="mord">0</span><span class="mord">0</span></span></span></span></span>，<span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mn>1</mn><mo>≤</mo><mi>k</mi><mo>≤</mo><mi>min</mi><mo>⁡</mo><mo stretchy="false">(</mo><mi>n</mi><mo separator="true">,</mo><mn>100</mn><mo stretchy="false">)</mo></mrow><annotation encoding="application/x-tex">1\le k\le \min(n,100)</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:0.78041em;vertical-align:-0.13597em;"></span><span class="mord">1</span><span class="mspace" style="margin-right:0.2777777777777778em;"></span><span class="mrel">≤</span><span class="mspace" style="margin-right:0.2777777777777778em;"></span></span><span class="base"><span class="strut" style="height:0.83041em;vertical-align:-0.13597em;"></span><span class="mord mathnormal" style="margin-right:0.03148em;">k</span><span class="mspace" style="margin-right:0.2777777777777778em;"></span><span class="mrel">≤</span><span class="mspace" style="margin-right:0.2777777777777778em;"></span></span><span class="base"><span class="strut" style="height:1em;vertical-align:-0.25em;"></span><span class="mop">min</span><span class="mopen">(</span><span class="mord mathnormal">n</span><span class="mpunct">,</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="mord">1</span><span class="mord">0</span><span class="mord">0</span><span class="mclose">)</span></span></span></span></span>。</li>
</ul>
<h4>做法</h4>
<p>设 <span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>d</mi><msub><mi>p</mi><mrow><mi>i</mi><mo separator="true">,</mo><mi>j</mi></mrow></msub></mrow><annotation encoding="application/x-tex">dp_{i,j}</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:0.980548em;vertical-align:-0.286108em;"></span><span class="mord mathnormal">d</span><span class="mord"><span class="mord mathnormal">p</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:0.311664em;"><span style="top:-2.5500000000000003em;margin-left:0em;margin-right:0.05em;"><span class="pstrut" style="height:2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mtight"><span class="mord mathnormal mtight">i</span><span class="mpunct mtight">,</span><span class="mord mathnormal mtight" style="margin-right:0.05724em;">j</span></span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.286108em;"><span></span></span></span></span></span></span></span></span></span></span> 为前 <span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>i</mi></mrow><annotation encoding="application/x-tex">i</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:0.65952em;vertical-align:0em;"></span><span class="mord mathnormal">i</span></span></span></span></span> 个数分成 <span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>j</mi></mrow><annotation encoding="application/x-tex">j</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:0.85396em;vertical-align:-0.19444em;"></span><span class="mord mathnormal" style="margin-right:0.05724em;">j</span></span></span></span></span> 个连续段需要的最小代价。</p>
<p>枚举最后一段的长度，可推出式子：<span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>d</mi><msub><mi>p</mi><mrow><mi>i</mi><mo separator="true">,</mo><mi>j</mi></mrow></msub><mo>=</mo><msubsup><mo><mi>min</mi><mo>⁡</mo></mo><mrow><mi>k</mi><mo>=</mo><mn>1</mn></mrow><mi>i</mi></msubsup><mo stretchy="false">(</mo><mi>d</mi><msub><mi>p</mi><mrow><mi>k</mi><mo>−</mo><mn>1</mn><mo separator="true">,</mo><mi>j</mi><mo>−</mo><mn>1</mn></mrow></msub><mo>+</mo><mi>c</mi><mi>o</mi><mi>s</mi><mi>t</mi><mo stretchy="false">(</mo><mi>k</mi><mo separator="true">,</mo><mi>i</mi><mo stretchy="false">)</mo><mo stretchy="false">)</mo></mrow><annotation encoding="application/x-tex">dp_{i,j}=\min\limits_{k=1}^i(dp_{k-1,j-1}+cost(k,i))</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:0.980548em;vertical-align:-0.286108em;"></span><span class="mord mathnormal">d</span><span class="mord"><span class="mord mathnormal">p</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:0.311664em;"><span style="top:-2.5500000000000003em;margin-left:0em;margin-right:0.05em;"><span class="pstrut" style="height:2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mtight"><span class="mord mathnormal mtight">i</span><span class="mpunct mtight">,</span><span class="mord mathnormal mtight" style="margin-right:0.05724em;">j</span></span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.286108em;"><span></span></span></span></span></span></span><span class="mspace" style="margin-right:0.2777777777777778em;"></span><span class="mrel">=</span><span class="mspace" style="margin-right:0.2777777777777778em;"></span></span><span class="base"><span class="strut" style="height:2.1816319999999996em;vertical-align:-0.752108em;"></span><span class="mop op-limits"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:1.4295239999999998em;"><span style="top:-2.347892em;margin-left:0em;"><span class="pstrut" style="height:3em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mtight"><span class="mord mathnormal mtight" style="margin-right:0.03148em;">k</span><span class="mrel mtight">=</span><span class="mord mtight">1</span></span></span></span><span style="top:-3em;"><span class="pstrut" style="height:3em;"></span><span><span class="mop">min</span></span></span><span style="top:-3.86786em;margin-left:0em;"><span class="pstrut" style="height:3em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mathnormal mtight">i</span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.752108em;"><span></span></span></span></span></span><span class="mopen">(</span><span class="mord mathnormal">d</span><span class="mord"><span class="mord mathnormal">p</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:0.3361079999999999em;"><span style="top:-2.5500000000000003em;margin-left:0em;margin-right:0.05em;"><span class="pstrut" style="height:2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mtight"><span class="mord mathnormal mtight" style="margin-right:0.03148em;">k</span><span class="mbin mtight">−</span><span class="mord mtight">1</span><span class="mpunct mtight">,</span><span class="mord mathnormal mtight" style="margin-right:0.05724em;">j</span><span class="mbin mtight">−</span><span class="mord mtight">1</span></span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.286108em;"><span></span></span></span></span></span></span><span class="mspace" style="margin-right:0.2222222222222222em;"></span><span class="mbin">+</span><span class="mspace" style="margin-right:0.2222222222222222em;"></span></span><span class="base"><span class="strut" style="height:1em;vertical-align:-0.25em;"></span><span class="mord mathnormal">c</span><span class="mord mathnormal">o</span><span class="mord mathnormal">s</span><span class="mord mathnormal">t</span><span class="mopen">(</span><span class="mord mathnormal" style="margin-right:0.03148em;">k</span><span class="mpunct">,</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="mord mathnormal">i</span><span class="mclose">)</span><span class="mclose">)</span></span></span></span></span>，其中 <span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>c</mi><mi>o</mi><mi>s</mi><mi>t</mi><mo stretchy="false">(</mo><mi>i</mi><mo separator="true">,</mo><mi>j</mi><mo stretchy="false">)</mo></mrow><annotation encoding="application/x-tex">cost(i,j)</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:1em;vertical-align:-0.25em;"></span><span class="mord mathnormal">c</span><span class="mord mathnormal">o</span><span class="mord mathnormal">s</span><span class="mord mathnormal">t</span><span class="mopen">(</span><span class="mord mathnormal">i</span><span class="mpunct">,</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="mord mathnormal" style="margin-right:0.05724em;">j</span><span class="mclose">)</span></span></span></span></span> 表示把 <span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><msub><mi>a</mi><mi>i</mi></msub><mo separator="true">,</mo><msub><mi>a</mi><mrow><mi>i</mi><mo>+</mo><mn>1</mn></mrow></msub><mo separator="true">,</mo><mo>…</mo><mo separator="true">,</mo><msub><mi>a</mi><mrow><mi>j</mi><mo>−</mo><mn>1</mn></mrow></msub><mo separator="true">,</mo><msub><mi>a</mi><mi>j</mi></msub></mrow><annotation encoding="application/x-tex">a_i,a_{i+1},\dots,a_{j-1},a_j</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:0.716668em;vertical-align:-0.286108em;"></span><span class="mord"><span class="mord mathnormal">a</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:0.31166399999999994em;"><span style="top:-2.5500000000000003em;margin-left:0em;margin-right:0.05em;"><span class="pstrut" style="height:2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mathnormal mtight">i</span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.15em;"><span></span></span></span></span></span></span><span class="mpunct">,</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="mord"><span class="mord mathnormal">a</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:0.311664em;"><span style="top:-2.5500000000000003em;margin-left:0em;margin-right:0.05em;"><span class="pstrut" style="height:2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mtight"><span class="mord mathnormal mtight">i</span><span class="mbin mtight">+</span><span class="mord mtight">1</span></span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.208331em;"><span></span></span></span></span></span></span><span class="mpunct">,</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="minner">…</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="mpunct">,</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="mord"><span class="mord mathnormal">a</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:0.311664em;"><span style="top:-2.5500000000000003em;margin-left:0em;margin-right:0.05em;"><span class="pstrut" style="height:2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mtight"><span class="mord mathnormal mtight" style="margin-right:0.05724em;">j</span><span class="mbin mtight">−</span><span class="mord mtight">1</span></span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.286108em;"><span></span></span></span></span></span></span><span class="mpunct">,</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="mord"><span class="mord mathnormal">a</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:0.311664em;"><span style="top:-2.5500000000000003em;margin-left:0em;margin-right:0.05em;"><span class="pstrut" style="height:2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mathnormal mtight" style="margin-right:0.05724em;">j</span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.286108em;"><span></span></span></span></span></span></span></span></span></span></span> 划成一段的代价。</p>
<p>考虑 <span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>c</mi><mi>o</mi><mi>s</mi><mi>t</mi><mo stretchy="false">(</mo><mi>i</mi><mo separator="true">,</mo><mi>j</mi><mo stretchy="false">)</mo></mrow><annotation encoding="application/x-tex">cost(i,j)</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:1em;vertical-align:-0.25em;"></span><span class="mord mathnormal">c</span><span class="mord mathnormal">o</span><span class="mord mathnormal">s</span><span class="mord mathnormal">t</span><span class="mopen">(</span><span class="mord mathnormal">i</span><span class="mpunct">,</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="mord mathnormal" style="margin-right:0.05724em;">j</span><span class="mclose">)</span></span></span></span></span> 的求法。</p>
<p>若 <span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><msub><mi>a</mi><mi>i</mi></msub><mo separator="true">,</mo><msub><mi>a</mi><mrow><mi>i</mi><mo>+</mo><mn>1</mn></mrow></msub><mo separator="true">,</mo><mo>…</mo><mo separator="true">,</mo><msub><mi>a</mi><mrow><mi>j</mi><mo>−</mo><mn>1</mn></mrow></msub></mrow><annotation encoding="application/x-tex">a_i,a_{i+1},\dots,a_{j-1}</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:0.716668em;vertical-align:-0.286108em;"></span><span class="mord"><span class="mord mathnormal">a</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:0.31166399999999994em;"><span style="top:-2.5500000000000003em;margin-left:0em;margin-right:0.05em;"><span class="pstrut" style="height:2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mathnormal mtight">i</span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.15em;"><span></span></span></span></span></span></span><span class="mpunct">,</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="mord"><span class="mord mathnormal">a</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:0.311664em;"><span style="top:-2.5500000000000003em;margin-left:0em;margin-right:0.05em;"><span class="pstrut" style="height:2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mtight"><span class="mord mathnormal mtight">i</span><span class="mbin mtight">+</span><span class="mord mtight">1</span></span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.208331em;"><span></span></span></span></span></span></span><span class="mpunct">,</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="minner">…</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="mpunct">,</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="mord"><span class="mord mathnormal">a</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:0.311664em;"><span style="top:-2.5500000000000003em;margin-left:0em;margin-right:0.05em;"><span class="pstrut" style="height:2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mtight"><span class="mord mathnormal mtight" style="margin-right:0.05724em;">j</span><span class="mbin mtight">−</span><span class="mord mtight">1</span></span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.286108em;"><span></span></span></span></span></span></span></span></span></span></span> 中有任意数等于 <span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><msub><mi>a</mi><mi>j</mi></msub></mrow><annotation encoding="application/x-tex">a_j</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:0.716668em;vertical-align:-0.286108em;"></span><span class="mord"><span class="mord mathnormal">a</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:0.311664em;"><span style="top:-2.5500000000000003em;margin-left:0em;margin-right:0.05em;"><span class="pstrut" style="height:2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mathnormal mtight" style="margin-right:0.05724em;">j</span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.286108em;"><span></span></span></span></span></span></span></span></span></span></span>，则 <span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>c</mi><mi>o</mi><mi>s</mi><mi>t</mi><mo stretchy="false">(</mo><mi>i</mi><mo separator="true">,</mo><mi>j</mi><mo stretchy="false">)</mo><mo>=</mo><mi>c</mi><mi>o</mi><mi>s</mi><mi>t</mi><mo stretchy="false">(</mo><mi>i</mi><mo separator="true">,</mo><mi>j</mi><mo>−</mo><mn>1</mn><mo stretchy="false">)</mo><mo>+</mo><mi>j</mi><mo>−</mo><mi>l</mi><mi>a</mi><mi>s</mi><msub><mi>t</mi><msub><mi>a</mi><mi>j</mi></msub></msub></mrow><annotation encoding="application/x-tex">cost(i,j)=cost(i,j-1)+j-last_{a_j}</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:1em;vertical-align:-0.25em;"></span><span class="mord mathnormal">c</span><span class="mord mathnormal">o</span><span class="mord mathnormal">s</span><span class="mord mathnormal">t</span><span class="mopen">(</span><span class="mord mathnormal">i</span><span class="mpunct">,</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="mord mathnormal" style="margin-right:0.05724em;">j</span><span class="mclose">)</span><span class="mspace" style="margin-right:0.2777777777777778em;"></span><span class="mrel">=</span><span class="mspace" style="margin-right:0.2777777777777778em;"></span></span><span class="base"><span class="strut" style="height:1em;vertical-align:-0.25em;"></span><span class="mord mathnormal">c</span><span class="mord mathnormal">o</span><span class="mord mathnormal">s</span><span class="mord mathnormal">t</span><span class="mopen">(</span><span class="mord mathnormal">i</span><span class="mpunct">,</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="mord mathnormal" style="margin-right:0.05724em;">j</span><span class="mspace" style="margin-right:0.2222222222222222em;"></span><span class="mbin">−</span><span class="mspace" style="margin-right:0.2222222222222222em;"></span></span><span class="base"><span class="strut" style="height:1em;vertical-align:-0.25em;"></span><span class="mord">1</span><span class="mclose">)</span><span class="mspace" style="margin-right:0.2222222222222222em;"></span><span class="mbin">+</span><span class="mspace" style="margin-right:0.2222222222222222em;"></span></span><span class="base"><span class="strut" style="height:0.85396em;vertical-align:-0.19444em;"></span><span class="mord mathnormal" style="margin-right:0.05724em;">j</span><span class="mspace" style="margin-right:0.2222222222222222em;"></span><span class="mbin">−</span><span class="mspace" style="margin-right:0.2222222222222222em;"></span></span><span class="base"><span class="strut" style="height:1.04176em;vertical-align:-0.34731999999999996em;"></span><span class="mord mathnormal" style="margin-right:0.01968em;">l</span><span class="mord mathnormal">a</span><span class="mord mathnormal">s</span><span class="mord"><span class="mord mathnormal">t</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:0.15139200000000003em;"><span style="top:-2.5500000000000003em;margin-left:0em;margin-right:0.05em;"><span class="pstrut" style="height:2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mtight"><span class="mord mtight"><span class="mord mathnormal mtight">a</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:0.3280857142857143em;"><span style="top:-2.357em;margin-left:0em;margin-right:0.07142857142857144em;"><span class="pstrut" style="height:2.5em;"></span><span class="sizing reset-size3 size1 mtight"><span class="mord mathnormal mtight" style="margin-right:0.05724em;">j</span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.2818857142857143em;"><span></span></span></span></span></span></span></span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.34731999999999996em;"><span></span></span></span></span></span></span></span></span></span></span>，其中 <span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>l</mi><mi>a</mi><mi>s</mi><msub><mi>t</mi><msub><mi>a</mi><mi>j</mi></msub></msub></mrow><annotation encoding="application/x-tex">last_{a_j}</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:1.04176em;vertical-align:-0.34731999999999996em;"></span><span class="mord mathnormal" style="margin-right:0.01968em;">l</span><span class="mord mathnormal">a</span><span class="mord mathnormal">s</span><span class="mord"><span class="mord mathnormal">t</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:0.15139200000000003em;"><span style="top:-2.5500000000000003em;margin-left:0em;margin-right:0.05em;"><span class="pstrut" style="height:2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mtight"><span class="mord mtight"><span class="mord mathnormal mtight">a</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:0.3280857142857143em;"><span style="top:-2.357em;margin-left:0em;margin-right:0.07142857142857144em;"><span class="pstrut" style="height:2.5em;"></span><span class="sizing reset-size3 size1 mtight"><span class="mord mathnormal mtight" style="margin-right:0.05724em;">j</span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.2818857142857143em;"><span></span></span></span></span></span></span></span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.34731999999999996em;"><span></span></span></span></span></span></span></span></span></span></span> 表示 <span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><msub><mi>a</mi><mi>j</mi></msub></mrow><annotation encoding="application/x-tex">a_j</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:0.716668em;vertical-align:-0.286108em;"></span><span class="mord"><span class="mord mathnormal">a</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:0.311664em;"><span style="top:-2.5500000000000003em;margin-left:0em;margin-right:0.05em;"><span class="pstrut" style="height:2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mathnormal mtight" style="margin-right:0.05724em;">j</span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.286108em;"><span></span></span></span></span></span></span></span></span></span></span> 最后一次出现的索引。</p>
<p>若没有，则显然 <span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>c</mi><mi>o</mi><mi>s</mi><mi>t</mi><mo stretchy="false">(</mo><mi>i</mi><mo separator="true">,</mo><mi>j</mi><mo stretchy="false">)</mo><mo>=</mo><mi>c</mi><mi>o</mi><mi>s</mi><mi>t</mi><mo stretchy="false">(</mo><mi>i</mi><mo separator="true">,</mo><mi>j</mi><mo>−</mo><mn>1</mn><mo stretchy="false">)</mo></mrow><annotation encoding="application/x-tex">cost(i,j)=cost(i,j-1)</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:1em;vertical-align:-0.25em;"></span><span class="mord mathnormal">c</span><span class="mord mathnormal">o</span><span class="mord mathnormal">s</span><span class="mord mathnormal">t</span><span class="mopen">(</span><span class="mord mathnormal">i</span><span class="mpunct">,</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="mord mathnormal" style="margin-right:0.05724em;">j</span><span class="mclose">)</span><span class="mspace" style="margin-right:0.2777777777777778em;"></span><span class="mrel">=</span><span class="mspace" style="margin-right:0.2777777777777778em;"></span></span><span class="base"><span class="strut" style="height:1em;vertical-align:-0.25em;"></span><span class="mord mathnormal">c</span><span class="mord mathnormal">o</span><span class="mord mathnormal">s</span><span class="mord mathnormal">t</span><span class="mopen">(</span><span class="mord mathnormal">i</span><span class="mpunct">,</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="mord mathnormal" style="margin-right:0.05724em;">j</span><span class="mspace" style="margin-right:0.2222222222222222em;"></span><span class="mbin">−</span><span class="mspace" style="margin-right:0.2222222222222222em;"></span></span><span class="base"><span class="strut" style="height:1em;vertical-align:-0.25em;"></span><span class="mord">1</span><span class="mclose">)</span></span></span></span></span>。</p>
<p>然后就得到了一个 <span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>O</mi><mo stretchy="false">(</mo><mi>k</mi><msup><mi>n</mi><mn>2</mn></msup><mo stretchy="false">)</mo></mrow><annotation encoding="application/x-tex">O(kn^2)</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:1.064108em;vertical-align:-0.25em;"></span><span class="mord mathnormal" style="margin-right:0.02778em;">O</span><span class="mopen">(</span><span class="mord mathnormal" style="margin-right:0.03148em;">k</span><span class="mord"><span class="mord mathnormal">n</span><span class="msupsub"><span class="vlist-t"><span class="vlist-r"><span class="vlist" style="height:0.8141079999999999em;"><span style="top:-3.063em;margin-right:0.05em;"><span class="pstrut" style="height:2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mtight">2</span></span></span></span></span></span></span></span><span class="mclose">)</span></span></span></span></span> 的做法。</p>
<p>考虑优化，观察递推式 <span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>d</mi><msub><mi>p</mi><mrow><mi>i</mi><mo separator="true">,</mo><mi>j</mi></mrow></msub><mo>=</mo><msubsup><mo><mi>min</mi><mo>⁡</mo></mo><mrow><mi>k</mi><mo>=</mo><mn>1</mn></mrow><mi>i</mi></msubsup><mo stretchy="false">(</mo><mi>d</mi><msub><mi>p</mi><mrow><mi>k</mi><mo>−</mo><mn>1</mn><mo separator="true">,</mo><mi>j</mi><mo>−</mo><mn>1</mn></mrow></msub><mo>+</mo><mi>c</mi><mi>o</mi><mi>s</mi><mi>t</mi><mo stretchy="false">(</mo><mi>k</mi><mo separator="true">,</mo><mi>i</mi><mo stretchy="false">)</mo><mo stretchy="false">)</mo></mrow><annotation encoding="application/x-tex">dp_{i,j}=\min\limits_{k=1}^i(dp_{k-1,j-1}+cost(k,i))</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:0.980548em;vertical-align:-0.286108em;"></span><span class="mord mathnormal">d</span><span class="mord"><span class="mord mathnormal">p</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:0.311664em;"><span style="top:-2.5500000000000003em;margin-left:0em;margin-right:0.05em;"><span class="pstrut" style="height:2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mtight"><span class="mord mathnormal mtight">i</span><span class="mpunct mtight">,</span><span class="mord mathnormal mtight" style="margin-right:0.05724em;">j</span></span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.286108em;"><span></span></span></span></span></span></span><span class="mspace" style="margin-right:0.2777777777777778em;"></span><span class="mrel">=</span><span class="mspace" style="margin-right:0.2777777777777778em;"></span></span><span class="base"><span class="strut" style="height:2.1816319999999996em;vertical-align:-0.752108em;"></span><span class="mop op-limits"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:1.4295239999999998em;"><span style="top:-2.347892em;margin-left:0em;"><span class="pstrut" style="height:3em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mtight"><span class="mord mathnormal mtight" style="margin-right:0.03148em;">k</span><span class="mrel mtight">=</span><span class="mord mtight">1</span></span></span></span><span style="top:-3em;"><span class="pstrut" style="height:3em;"></span><span><span class="mop">min</span></span></span><span style="top:-3.86786em;margin-left:0em;"><span class="pstrut" style="height:3em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mathnormal mtight">i</span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.752108em;"><span></span></span></span></span></span><span class="mopen">(</span><span class="mord mathnormal">d</span><span class="mord"><span class="mord mathnormal">p</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:0.3361079999999999em;"><span style="top:-2.5500000000000003em;margin-left:0em;margin-right:0.05em;"><span class="pstrut" style="height:2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mtight"><span class="mord mathnormal mtight" style="margin-right:0.03148em;">k</span><span class="mbin mtight">−</span><span class="mord mtight">1</span><span class="mpunct mtight">,</span><span class="mord mathnormal mtight" style="margin-right:0.05724em;">j</span><span class="mbin mtight">−</span><span class="mord mtight">1</span></span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.286108em;"><span></span></span></span></span></span></span><span class="mspace" style="margin-right:0.2222222222222222em;"></span><span class="mbin">+</span><span class="mspace" style="margin-right:0.2222222222222222em;"></span></span><span class="base"><span class="strut" style="height:1em;vertical-align:-0.25em;"></span><span class="mord mathnormal">c</span><span class="mord mathnormal">o</span><span class="mord mathnormal">s</span><span class="mord mathnormal">t</span><span class="mopen">(</span><span class="mord mathnormal" style="margin-right:0.03148em;">k</span><span class="mpunct">,</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="mord mathnormal">i</span><span class="mclose">)</span><span class="mclose">)</span></span></span></span></span>，<span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>d</mi><msub><mi>p</mi><mrow><mi>i</mi><mo>+</mo><mn>1</mn><mo separator="true">,</mo><mi>j</mi></mrow></msub><mo>=</mo><msubsup><mo><mi>min</mi><mo>⁡</mo></mo><mrow><mi>k</mi><mo>=</mo><mn>1</mn></mrow><mrow><mi>i</mi><mo>+</mo><mn>1</mn></mrow></msubsup><mo stretchy="false">(</mo><mi>d</mi><msub><mi>p</mi><mrow><mi>k</mi><mo>−</mo><mn>1</mn><mo separator="true">,</mo><mi>j</mi><mo>−</mo><mn>1</mn></mrow></msub><mo>+</mo><mi>c</mi><mi>o</mi><mi>s</mi><mi>t</mi><mo stretchy="false">(</mo><mi>k</mi><mo separator="true">,</mo><mi>i</mi><mo>+</mo><mn>1</mn><mo stretchy="false">)</mo><mo stretchy="false">)</mo></mrow><annotation encoding="application/x-tex">dp_{i+1,j}=\min\limits_{k=1}^{i+1}(dp_{k-1,j-1}+cost(k,i+1))</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:0.980548em;vertical-align:-0.286108em;"></span><span class="mord mathnormal">d</span><span class="mord"><span class="mord mathnormal">p</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:0.311664em;"><span style="top:-2.5500000000000003em;margin-left:0em;margin-right:0.05em;"><span class="pstrut" style="height:2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mtight"><span class="mord mathnormal mtight">i</span><span class="mbin mtight">+</span><span class="mord mtight">1</span><span class="mpunct mtight">,</span><span class="mord mathnormal mtight" style="margin-right:0.05724em;">j</span></span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.286108em;"><span></span></span></span></span></span></span><span class="mspace" style="margin-right:0.2777777777777778em;"></span><span class="mrel">=</span><span class="mspace" style="margin-right:0.2777777777777778em;"></span></span><span class="base"><span class="strut" style="height:2.181632em;vertical-align:-0.752108em;"></span><span class="mop op-limits"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:1.429524em;"><span style="top:-2.347892em;margin-left:0em;"><span class="pstrut" style="height:3em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mtight"><span class="mord mathnormal mtight" style="margin-right:0.03148em;">k</span><span class="mrel mtight">=</span><span class="mord mtight">1</span></span></span></span><span style="top:-3em;"><span class="pstrut" style="height:3em;"></span><span><span class="mop">min</span></span></span><span style="top:-3.86786em;margin-left:0em;"><span class="pstrut" style="height:3em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mtight"><span class="mord mathnormal mtight">i</span><span class="mbin mtight">+</span><span class="mord mtight">1</span></span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.752108em;"><span></span></span></span></span></span><span class="mopen">(</span><span class="mord mathnormal">d</span><span class="mord"><span class="mord mathnormal">p</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:0.3361079999999999em;"><span style="top:-2.5500000000000003em;margin-left:0em;margin-right:0.05em;"><span class="pstrut" style="height:2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mtight"><span class="mord mathnormal mtight" style="margin-right:0.03148em;">k</span><span class="mbin mtight">−</span><span class="mord mtight">1</span><span class="mpunct mtight">,</span><span class="mord mathnormal mtight" style="margin-right:0.05724em;">j</span><span class="mbin mtight">−</span><span class="mord mtight">1</span></span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.286108em;"><span></span></span></span></span></span></span><span class="mspace" style="margin-right:0.2222222222222222em;"></span><span class="mbin">+</span><span class="mspace" style="margin-right:0.2222222222222222em;"></span></span><span class="base"><span class="strut" style="height:1em;vertical-align:-0.25em;"></span><span class="mord mathnormal">c</span><span class="mord mathnormal">o</span><span class="mord mathnormal">s</span><span class="mord mathnormal">t</span><span class="mopen">(</span><span class="mord mathnormal" style="margin-right:0.03148em;">k</span><span class="mpunct">,</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="mord mathnormal">i</span><span class="mspace" style="margin-right:0.2222222222222222em;"></span><span class="mbin">+</span><span class="mspace" style="margin-right:0.2222222222222222em;"></span></span><span class="base"><span class="strut" style="height:1em;vertical-align:-0.25em;"></span><span class="mord">1</span><span class="mclose">)</span><span class="mclose">)</span></span></span></span></span>，发现前半部分 <span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>d</mi><msub><mi>p</mi><mrow><mi>k</mi><mo>−</mo><mn>1</mn><mo separator="true">,</mo><mi>j</mi><mo>−</mo><mn>1</mn></mrow></msub></mrow><annotation encoding="application/x-tex">dp_{k-1,j-1}</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:0.980548em;vertical-align:-0.286108em;"></span><span class="mord mathnormal">d</span><span class="mord"><span class="mord mathnormal">p</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:0.3361079999999999em;"><span style="top:-2.5500000000000003em;margin-left:0em;margin-right:0.05em;"><span class="pstrut" style="height:2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mtight"><span class="mord mathnormal mtight" style="margin-right:0.03148em;">k</span><span class="mbin mtight">−</span><span class="mord mtight">1</span><span class="mpunct mtight">,</span><span class="mord mathnormal mtight" style="margin-right:0.05724em;">j</span><span class="mbin mtight">−</span><span class="mord mtight">1</span></span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.286108em;"><span></span></span></span></span></span></span></span></span></span></span> 是不变的，后半部分当且仅当 <span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>k</mi><mo>≤</mo><mi>l</mi><mi>a</mi><mi>s</mi><msub><mi>t</mi><msub><mi>a</mi><mrow><mi>i</mi><mo>+</mo><mn>1</mn></mrow></msub></msub></mrow><annotation encoding="application/x-tex">k\le last_{a_{i+1}}</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:0.83041em;vertical-align:-0.13597em;"></span><span class="mord mathnormal" style="margin-right:0.03148em;">k</span><span class="mspace" style="margin-right:0.2777777777777778em;"></span><span class="mrel">≤</span><span class="mspace" style="margin-right:0.2777777777777778em;"></span></span><span class="base"><span class="strut" style="height:0.986205em;vertical-align:-0.291765em;"></span><span class="mord mathnormal" style="margin-right:0.01968em;">l</span><span class="mord mathnormal">a</span><span class="mord mathnormal">s</span><span class="mord"><span class="mord mathnormal">t</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:0.15139200000000003em;"><span style="top:-2.5500000000000003em;margin-left:0em;margin-right:0.05em;"><span class="pstrut" style="height:2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mtight"><span class="mord mtight"><span class="mord mathnormal mtight">a</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:0.32808571428571426em;"><span style="top:-2.357em;margin-left:0em;margin-right:0.07142857142857144em;"><span class="pstrut" style="height:2.5em;"></span><span class="sizing reset-size3 size1 mtight"><span class="mord mtight"><span class="mord mathnormal mtight">i</span><span class="mbin mtight">+</span><span class="mord mtight">1</span></span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.20252142857142857em;"><span></span></span></span></span></span></span></span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.291765em;"><span></span></span></span></span></span></span></span></span></span></span> 时才会改变，且改变的值恒为 <span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>j</mi><mo>−</mo><mi>l</mi><mi>a</mi><mi>s</mi><msub><mi>t</mi><msub><mi>a</mi><mi>j</mi></msub></msub></mrow><annotation encoding="application/x-tex">j-last_{a_j}</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:0.85396em;vertical-align:-0.19444em;"></span><span class="mord mathnormal" style="margin-right:0.05724em;">j</span><span class="mspace" style="margin-right:0.2222222222222222em;"></span><span class="mbin">−</span><span class="mspace" style="margin-right:0.2222222222222222em;"></span></span><span class="base"><span class="strut" style="height:1.04176em;vertical-align:-0.34731999999999996em;"></span><span class="mord mathnormal" style="margin-right:0.01968em;">l</span><span class="mord mathnormal">a</span><span class="mord mathnormal">s</span><span class="mord"><span class="mord mathnormal">t</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:0.15139200000000003em;"><span style="top:-2.5500000000000003em;margin-left:0em;margin-right:0.05em;"><span class="pstrut" style="height:2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mtight"><span class="mord mtight"><span class="mord mathnormal mtight">a</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height:0.3280857142857143em;"><span style="top:-2.357em;margin-left:0em;margin-right:0.07142857142857144em;"><span class="pstrut" style="height:2.5em;"></span><span class="sizing reset-size3 size1 mtight"><span class="mord mathnormal mtight" style="margin-right:0.05724em;">j</span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.2818857142857143em;"><span></span></span></span></span></span></span></span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height:0.34731999999999996em;"><span></span></span></span></span></span></span></span></span></span></span>。</p>
<p>可以考虑维护一个数据结构，支持修改一个数，区间加一个数，区间求最小值。</p>
<p>直接线段树，复杂度已经是 <span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mi>O</mi><mo stretchy="false">(</mo><mi>k</mi><mi>n</mi><mi>log</mi><mo>⁡</mo><mi>n</mi><mo stretchy="false">)</mo></mrow><annotation encoding="application/x-tex">O(kn\log n)</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:1em;vertical-align:-0.25em;"></span><span class="mord mathnormal" style="margin-right:0.02778em;">O</span><span class="mopen">(</span><span class="mord mathnormal" style="margin-right:0.03148em;">k</span><span class="mord mathnormal">n</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="mop">lo<span style="margin-right:0.01389em;">g</span></span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="mord mathnormal">n</span><span class="mclose">)</span></span></span></span></span>，可过。</p>

### 代码

```cpp
/**************************************************************
 * Problem: codeforces 1527E
 * Author: 芊枫
 * Date: 2021 July 18
 * Algorithm: 线段树优化DP
 **************************************************************/
#include <bits/stdc++.h>
#include <cmath>
#include <cstring>
#include <new>
#define INF 0x3f3f3f3f3f3f3f3f
#define IINF 0x3f3f3f3f

using namespace std;

inline int read() {
  int x = 0;
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
void write(const int &x) {
  if (!x) {
    putchar('0');
    return;
  }
  char f[100];
  int tmp = x;
  if (tmp < 0) {
    tmp = -tmp;
    putchar('-');
  }
  int s = 0;
  while (tmp > 0) {
    f[s++] = tmp % 10 + '0';
    tmp /= 10;
  }
  while (s > 0) {
    putchar(f[--s]);
  }
}

const int maxN = 35090;
int totN;
int totK;
int nums[maxN];
int DP[maxN][120];
int last[maxN];
int totMIN = IINF;

struct Node {
  int l, r;
  int val, tag;
  Node *lch, *rch;
  inline void pushup() { val = min(lch->val, rch->val); }
  Node(int L, int R) {
    l = L;
    r = R;
    val = 0;
    tag = 0;
    if (l == r) {
      return;
    }
    int Mid = (L + R) >> 1;
    lch = new Node(L, Mid);
    rch = new Node(Mid + 1, R);
    pushup();
  }
  inline void maketag(int w) {
    val += w;
    tag += w;
  }
  void pushdown() {
    if (!tag) {
      return;
    }
    lch->maketag(tag);
    rch->maketag(tag);
    tag = 0;
  }
  inline bool INrange(int L, int R) { return (L <= l) && (r <= R); }
  inline bool OUTrange(int L, int R) { return (L > r) || (l > R); }
  void update(int L, int R, int w) {
    if (INrange(L, R)) {
      maketag(w);
    } else if (!OUTrange(L, R)) {
      pushdown();
      lch->update(L, R, w);
      rch->update(L, R, w);
      pushup();
    }
  }
  int query(int L, int R) {
    if (INrange(L, R)) {
      return val;
    } else if (OUTrange(L, R)) {
      return IINF;
    }
    pushdown();
    return min(lch->query(L, R), rch->query(L, R));
  }
};

Node *rot;

int main() {
  totN = read();
  totK = read();
  for (int i = 1; i <= totN; ++i) {
    nums[i] = read();
  }
  memset(DP, 0x3f, sizeof(DP));
  DP[0][0] = 0;
  for (int j = 1; j <= 109; ++j) {
    rot = new Node(1, totN);
    for (int i = 1; i <= totN; ++i) {
      if (j > min(i, totK)) {
        continue;
      }
      rot->update(i, i, DP[i - 1][j - 1]);
      if (last[nums[i]]) {
        rot->update(1, last[nums[i]], i - last[nums[i]]);
      }
      DP[i][j] = rot->query(1, i);
      last[nums[i]] = i;
    }
    memset(last, 0, sizeof last);
  }
  for (int i = 1; i <= totK; ++i) {
    totMIN = min(totMIN, DP[totN][i]);
  }
  write(totMIN);
  return 0;
} // Thomitics Code
```


## 练习5

### 题目

维护一个长度为 n 的数组 a[]，支持：

1. 单点修改。

2. 给定 l,r，查询$max_{l\leq x<y\leq r}a[y]-a[x]$。

### 思路

每个点维护三个值，$max$,$min$,$max_{l\leq x<y\leq r}a[y]-a[x]$。

合并两个节点时有三种情况：

1. 两个点都在左边。
   
   $max_{l\leq x<y\leq r}a[y]-a[x]$ = 左孩子的这个值。
2. 两个点都在右边。

    $max_{l\leq x<y\leq r}a[y]-a[x]$ = 右孩子的这个值。
3. 两个点分别在两边。
   
    使用右边的最大值减去左边的最小值。

合并时找到这三个值取$max$即可。

### 代码

这个题比较简单，就不写代码了。

### 拓展

转移到树上？[这样](http://poj.org/problem?id=3728)

树链剖分即可。

## 练习6

### 题目

In a galaxy far, far away, there are two integer sequence a and b of length n.

b is a static permutation of 1 to n. Initially a is filled with zeroes.

There are two kind of operations:

1. `add l r`: add one for $a_l,a_{l+1}...a_r$
2. `query l r`: query $\sum_{i=l}^r \lfloor \frac{a_i}{b_i}\rfloor$
   
### 思路

考虑每次增加 $b_i$ 时 $\lfloor \frac{a_i}{b_i}\rfloor$ 的值才会改变，那么总变动次数最多是 $O(nlogn)$ 级别的。

于是线段树每个节点可以维护一个该区间内的元素最少再加几次就要被修改，如果当前是 $0$ 次的话就暴力 $\text{DFS}$ 下去修改即可。

这里有一个问题就是如何判断那个点应该修改了。

我们可以对于每一个点维护一个`flag`，记录这个点的子树中是否有应该修改的点，如果`flag==true`则DFS找出应该更改的那个点并清除标记，恢复树上的值即可。

易证这样的时间复杂度是均摊的，对于每一次修改它只均摊了 $O(logn)$ 的复杂度。

所以总时间复杂度 $O(nlog^2n)$。

### 代码

这个题也比较简单，就不写代码了。

## 练习7

### 题目

Rikka and Yuta are interested in Phi function (which is known as Euler's totient function).

Yuta gives Rikka an array $A[1..n]$ of positive integers,  then Yuta makes $m$ queries.  

There are three types of queries: 

$1   \; l  \; r$ 

Change $A[i]$  into $\varphi(A[i])$, for all $i \in [l, r]$.

$2 \; l  \; r  \; x$ 

Change $A[i]$ into $x$, for all $i \in [l, r]$.

$3   \; l  \; r$ 

Sum up $A[i]$, for all $i \in [l, r]$.

Help Rikka by computing the results of queries of type 3.

### 思路

这个题与很多题比较相似，就是可以发现一个数在取 $phi$ $log$ 次后就会变成 $1$。

考虑还有区间加的操作，于是随着操作的进行，元素有着变为相等的趋势。

考虑区间加操作可以暴力，主要的难点在区间 $phi$ 上。

考虑如下优化：如果当前这个区间的数相等，那么直接用这个数代替这个区间，进行 $phi$ 操作时直接将代表这个区间的数取 $phi$。

通过势能分析的方法可以得出这样做的复杂度不大于 $O(nlog^2n)$。

### 代码

```cpp
/**************************************************************
 * Problem: HDU 5634
 * Author: 芊枫
 * Date: 2021 July 18
 * Algorithm: 线段树Ex&线性筛
 **************************************************************/
#include <bits/stdc++.h>
#include <codecvt>
#include <cstdio>
#include <cstring>
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
long long totN;
long long totT;
long long totM;
long long nums[maxN];
long long Phi[maxN];

void GetPhi() {
  memset(Phi, 0, sizeof(Phi));
  Phi[1] = 1;
  for (long long i = 2; i < maxN; i++) {
    if (!Phi[i]) {
      for (long long j = i; j < maxN; j += i) {
        if (!Phi[j])
          Phi[j] = j;
        Phi[j] = Phi[j] / i * (i - 1);
      }
    }
  }
}

struct Node {
  long long l, r;
  long long sum;
  long long val, tag;
  bool same;
  Node *lch, *rch;
  inline void pushup() {
    sum = lch->sum + rch->sum;
    if (lch->val == rch->val && lch->same && rch->same && lch->val != -1) {
      same = true;
      val = lch->val;
    } else {
      same = false;
      val = -1;
    }
  }
  Node(long long L, long long R) {
    l = L;
    r = R;
    tag = 0;
    same = false;
    val = -1;
    if (l == r) {
      same = true;
      val = nums[l];
      sum = nums[l];
      lch = rch = NULL;
      return;
    }
    long long Mid = (L + R) >> 1;
    lch = new Node(L, Mid);
    rch = new Node(Mid + 1, R);
    pushup();
  }
  void pushdown() {
    if (l == r) {
      return;
    }
    if (same) {
      lch->same = rch->same = true;
      lch->val = rch->val = val;
      lch->sum = 1ll * (lch->r - lch->l + 1) * val;
      rch->sum = 1ll * (rch->r - rch->l + 1) * val;
    }
  }
  void update(long long L, long long R, long long w) {
    if (l == L && r == R) {
      same = true;
      val = w;
      sum = (R - L + 1) * 1ll * w;
      return;
    }
    if (l == r) {
      same = true;
      val = w;
      sum = w;
      return;
    }
    pushdown();
    if (lch->r >= R) {
      lch->update(L, R, w);
    }
    if (rch->l <= L) {
      rch->update(L, R, w);
    } else {
      lch->update(L, lch->r, w);
      rch->update(rch->l, R, w);
    }
    pushup();
  }
  void dify(long long L, long long R) {
    if (l == L && r == R && same) {
      val = Phi[val];
      sum = (r - l + 1) * 1ll * val;
      return;
    }
    pushdown();
    if (lch->r >= R) {
      lch->dify(L, R);
    } else if (rch->l <= L) {
      rch->dify(L, R);
    } else {
      lch->dify(L, lch->r);
      rch->dify(rch->l, R);
    }
    pushup();
  }
  // long long query(long long L, long long R) {
  //   if (l == L && r == R) {
  //     return sum;
  //   }
  //   pushdown();
  //   if (lch->r >= R) {
  //     return lch->query(L, R);
  //   } else if (rch->l <= R) {
  //     return rch->query(L, R);
  //   } else {
  //     return lch->query(L, lch->r) + rch->query(rch->l, R);
  //   }
  //   pushup();
  // }
  inline bool INrange(long long L, long long R) { return (L <= l) && (r <= R); }
  inline bool OUTrange(long long L, long long R) { return (l > R) || (L > r); }
  long long query(long long L, long long R) {
    if (INrange(L, R)) {
      return sum;
    } else if (OUTrange(L, R)) {
      return 0;
    }
    pushdown();
    return 1ll * (lch->query(L, R) + rch->query(L, R));
  }
};

int main() {
  totT = read();
  GetPhi();
  while (totT--) {
    memset(nums, 0, sizeof(nums));
    totN = read();
    totM = read();
    for (int i = 1; i <= totN; ++i) {
      nums[i] = read();
    }
    Node *rot = new Node(1, totN);
    for (int i = 1; i <= totM; ++i) {
      long long tmp, x, y, z;
      tmp = read();
      x = read();
      y = read();
      if (tmp == 1) {
        rot->dify(x, y);
      } else if (tmp == 2) {
        z = read();
        rot->update(x, y, z);
      } else {
        write(rot->query(x, y));
        putchar('\n');
      }
    }
    delete rot;
  }
  return 0;
} // Thomitics Code
```

## 练习8

### 题目

$n$个点，$m$个询问。

给你一棵树的括号序列，输出它的直径。

有$m$次询问，每次询问表示交换两个括号，输出交换两个括号后的直径（保证每次操作后都为一棵树）

输出共$m+1$行。

$3 \le n \le 100\,000,1 \le q \le 100\,000$

### 解释

什么是括号树？

![](https://cdn.luogu.com.cn/upload/vjudge_pic/CF1149C/285660de836d4f0c8cc3430ffe028ede0245c7ef.png)

就是这样。

### 思路

考虑一个点经过一个括号序列后到的点距离它的距离，其实就是将这个括号序列匹配的括号消掉，剩下来的括号的个数。
所以答案就是求出最大的子段，使得剩下的括号数量最多。

考虑线段树维护，主要难点是在合并两个区间上。

合并两个区间时，假设左边的右括号数为 a，左括号数为 b。右边的右括号数为 c，左括号数为 d。那么对答案的贡献是 a+abs(b-c)+d。即 max(a+b-c+d,a-b+c+d)。由于答案是求最大值，所以可以把 max 拆开来求。

由上式可以看出，对于每一个点只需要维护 后缀左括号+后缀右括号,后缀左括号-后缀右括号,前缀右括号+前缀左括号,前缀右括号-前缀左括号的最大值即可。

时间复杂度 O(qlogn)。
