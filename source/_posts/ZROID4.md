---
title: 正睿OI Day4 总结
date: 2021-07-20
tags:
 - 培训总结
 - 线段树
categories: 培训总结
description: 正睿OI Day4总结。
top_img: https://api.dujin.org/bing/1920.php
cover: https://tvax4.sinaimg.cn/large/0072Vf1pgy1foxkf96kuyj31hc0u04kb.jpg
---

今天主要是讲了一些题目，那就直接上题吧！

## T1

### 题目
要求支持树上三个操作：
 - 链加
 - 链乘
 - 链覆盖
 - 维护链的立方和。

### 思路
首先这道题是在树上，树链剖分即可变为在一条链上。

链加和链乘都很简单，主要是看后两个操作。

很显然，链覆盖操作优先级是最高的，链覆盖时，直接去掉前面所有标记，打上链覆盖标记即可。

立方和：这个很容易想到要拆开。拆开维护区间立方的和、平方的和、区间和即可。

## T2

### 题目
在二维平面内框一个矩阵，使得框内权值和最大。

 - 点的数量<=2000

### 思路
……使用扫描线的思想。先离散化，然后使用一条线从上往下扫描，经过点就加上，然后矩形内部维护区间最大子段和，用线段树维护，每个节点维护三个信息：这个节点内的最大子段和、这个节点和左边接壤的最大子段和、这个节点和右边接壤的最大子段和即可。

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
long long totT;
long long totN;
long long totL;

struct Node {
  long long l, r;
  long long lval, rval;
  Node *lch, *rch;
  long long mval;
  long long sum;
  void pushup() {
    sum = lch->sum + rch->sum;
    mval = max(lch->mval, rch->mval);
    mval = max(mval, lch->rval + rch->lval);
    lval = max(lch->lval, lch->sum + rch->lval);
    rval = max(rch->rval, rch->sum + lch->rval);
  }
  void pushup(Node *a, Node *b) {
    sum = a->sum + b->sum;
    mval = max(a->mval, b->mval);
    mval = max(mval, a->rval + b->lval);
    lval = max(a->lval, a->sum + b->lval);
    rval = max(b->rval, b->sum + a->rval);
  }
  Node(long long L, long long R) {
    l = L;
    r = R;
    mval = lval = rval = 0;
    if (l == r) {
      lch = rch = NULL;
      lval = rval = mval = 1;
      return;
    }
    long long Mid = (L + R) >> 1;
    lch = new Node(L, Mid);
    rch = new Node(Mid + 1, R);
    pushup();
  }
  void update(long long pos, long long w) {
    if (l == r) {
      sum = mval = lval = rval = w;
    }
    if (pos >= rch->l) {
      rch->update(pos, w);
    } else if (pos <= lch->r) {
      lch->update(pos, w);
    }
    pushup();
  }
  inline bool INrange(long long L, long long R) { return (L <= l) && (r <= R); }
  inline bool OUTrange(long long L, long long R) { return (l > R) || (L > r); }
  Node *query(long long L, long long R) {
    if (INrange(L, R)) {
      return this;
    } else if (R <= lch->r) {
      return lch->query(L, R);
    } else if (L >= rch->l) {
      return rch->query(L, R);
    }
    Node *ANS;
    ANS->pushup(lch->query(L, R), rch->query(L, R));
    return ANS;
  }
};

struct Poi {
  long long x;
  long long y;
  long long w;
} poison[maxN];

inline bool operator<(Poi a, Poi b) { return a.x < b.x; }

int main() {
  totT = read();
  while (totT--) {
    totN = read();
    for (int i = 1; i <= totN; ++i) {
      poison[i].x = read();
      poison[i].y = read();
      poison[i].w = read();
    }
    sort(poison + 1, poison + totN + 1);
    totL = unique(poison + 1, poison + totN + 1) - poison - 1;
    for (int i = 1; i <= totN; ++i) {
      poison[i].x =
          lower_bound((poison + 1)->x, (poison + totN + 1)->x, poison[i].x);
      poison[i].y =
          lower_bound((poison + 1)->y, (poison + totN + 1)->y, poison[i].y);
    }
    sort(poison + 1, poison + totN + 1);
    long long ANS = 0;
    for (int i = 1; i <= totL; ++i) {
      Node *rot = new Node(1, totL);
      long long Ad = 1;
      while (poison[Ad].x < i) {
        ++Ad;
      }
      for (int j = i; j <= totL; ++j) {
        while (Ad <= totN && poison[Ad].x == j) {
          rot->update(poison[Ad].y, poison[Ad].w);
          ++Ad;
        }
        ANS = max(ANS, rot->query(1, totL)->mval);
      }
    }
    write(ANS);
    putchar('\n');
  }
  return 0;
} // Thomitics Code

```

## 线段树LIS

离散化，然后：

$f[x]=\displaystyle \max_{a[y]<a[x]}{f[y]}+1$

使用线段树维护离散化后的数组区间最大值，然后从原序列上从前往后DP，单点修改即可。

## T3

### 题目

求一个数列中的逆序对个数
 - 逆序对 (i,j) 满足 i<j 且 ai>aj
 - N<=10^5

### 思路

类似的思路前面其实有说道，就是说使用线段树对于每一个数维护有几个，也就是权值线段树，然后从前往后扫描数列，对于每个新加入的数，查询前缀和，并且将它对应节点的权值+1即可。

## T4

### 题目

第一行有两个非负整数n和min。n表示下面有多少条命令，min表示工资下界（低于下届的员工直接被开除）。
接下来的n行，每行表示一条命令。命令可以是以下四种之一

![](https://i0.hdslb.com/bfs/album/a47c5576116cbcb8a63f978edf1ac2591e091a26.png)

### 思路

对于A操作，把所有工资对应位置-k，然后对于加上之后的位置权值+k即可。l命令直接权值+1.S命令和A操作基本相同。其实并不用担心退出公司这种操作，因为我们只需要不维护min以下的就可以了。

## T5

### 题目

可以选择把数列的前缀 x 个分给 k 个人，要求每个人都有，而且是连续的一段，求每个人得到的和的最大值最小是多少。

 - $1\leq T\leq 10$
 - $1\leq n\leq 2*10^5$
 - $1\leq k\leq n$
 - $-10^9\leq ai\leq 10^9$

## T6

### 题目

求 1～ (k-1) 里面需要把多少个数变成 0 ，可以使得 $\sum_{i=1}^k a_k \leq m$ 。

 - $1 \leq  Q \leq  15$
 - $1 \leq  n \leq  2*10^5$
 - $1 \leq  m \leq  10^9$
 - $\forall i, 1 \leq  W_i \leq  m$

### 思路

使用权值线段树。考虑将原式转化为$\displaystyle \sum_{选择几个} a_k \geq m$。然后在权值线段树上面一个一个加入，我们找的一定是前几小的数，二分查询即可。


## T7

### 题目

每次插入区间[Li, Ri]之间的数，查询中位数
 - $N<=400000$
 - $Li,Ri<=10^9$

### 思路
容易想到是权值线段树，二分查询排名$\frac{nowN}{2}$的数即可。但是由于数据范围比较大，所以要动态开点。

## T8

### 题目

给出一些从边界出发的直线，求平面被分割成了几块。
 - 矩阵大小 $n*m$ ，有 $k$ 条线
 - $1≤n,m≤10^9$
 - $1≤K≤10^5$

![](https://i0.hdslb.com/bfs/album/44c8e806f7add82e8b8addcc5cb5a6001267f730.png)

### 思路

观察例图或是手玩几组样例，得出其实面的数量就是十字数量+1.

那么怎么求十字数量呢？

其实跟扫描线差不多，我们从矩形上面开始扫，遇到竖线上面的点就+1，遇到竖线下端点就-1，遇到横线查询横线区间和就是此次贡献的十字数量。

## 线段树合并

### 思路
就是建立一棵新的线段树保存原有的两颗线段树的信息

操作过程（两个线段树 A 和 B）

 - 如果A有p位置，B没有，新的线段树p位置赋成A，返回 A
 - 如果B有p位置，A没有，新的线段树p位置赋成B，返回 B
 - 如果合并到两棵线段树共有的节点了
   - 按照所需合并A,B
   - 把新线段树上的p位置赋成A
   - 返回 A

### 代码

![](https://i0.hdslb.com/bfs/album/6d12122873537060e542206458b7d07bd12c5616.png)

## T9

### 题目

N 个结点的树，每个结点有一个颜色
求以一棵树的每个节点为根节点的子树中，出现次数最多的颜色的编号之和

 - $N<=10^5$ 
 - $颜色<=n$


### 思路

每个点一个权值线段树，维护子节点中出现最多的颜色出现次数&所有出现次数最多的颜色的编号之和，然后线段树合并至根节点即可。

## T10

### 题目

![](https://i0.hdslb.com/bfs/album/69ec2afb4d9dd4e3accaa1355bde7ceaf6a8b806.png)

### 思路


<div data-v-5a58a989="" data-v-245af330="" class="marked" data-v-fc9f7d52=""><p>这道题采用权值线段树合并的解法。</p>


<p>首先讲一下解法中出现的两个概念：权值线段树与线段树合并。</p>
<p>所谓权值线段树，可以理解为维护的信息反过来的普通线段树，我个人认为值域线段树这个名字其实要准确一些。</p>
<p>举个例子，我们将序列<span><span class="katex"><span class="katex-mathml"><math xmlns="http://www.w3.org/1998/Math/MathML"><semantics><mrow><mn>1</mn><mo separator="true">,</mo><mn>1</mn><mo separator="true">,</mo><mn>2</mn><mo separator="true">,</mo><mn>3</mn><mo separator="true">,</mo><mn>4</mn><mo separator="true">,</mo><mn>4</mn><mo separator="true">,</mo><mn>4</mn><mo separator="true">,</mo><mn>5</mn><mo separator="true">,</mo><mn>6</mn><mo separator="true">,</mo><mn>6</mn></mrow><annotation encoding="application/x-tex">1,1,2,3,4,4,4,5,6,6</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height:0.8388800000000001em;vertical-align:-0.19444em;"></span><span class="mord">1</span><span class="mpunct">,</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="mord">1</span><span class="mpunct">,</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="mord">2</span><span class="mpunct">,</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="mord">3</span><span class="mpunct">,</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="mord">4</span><span class="mpunct">,</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="mord">4</span><span class="mpunct">,</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="mord">4</span><span class="mpunct">,</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="mord">5</span><span class="mpunct">,</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="mord">6</span><span class="mpunct">,</span><span class="mspace" style="margin-right:0.16666666666666666em;"></span><span class="mord">6</span></span></span></span></span>中的数依次插入，那么插入完成之后的效果图大概是下面这样的：</p>
<p><img src="https://i0.hdslb.com/bfs/album/c65ba9c4b809c67a7a376c4cdfb6cf8d7c5ee79e.png" alt=""></p>
<p>（其中红色为节点的值）</p>
<p>也就是说，每一个节点维护的值是这个区间内的数出现的次数。</p>
<p>在实现权值线段树时，我们通常会采用动态开点的方式，也就是不创建无关的节点，当然也可以离散化数据，否则必然会空间超限。</p>
<p>而线段树合并的原理则是基于线段树较为稳定的结构。</p>
<p>在合并的过程中，我们将两颗线段树对应位置的节点的值合在一起，创建一颗新的线段树。</p>
<p>过程大致如下：</p>
<p><img src="https://i0.hdslb.com/bfs/album/6278fb44a2784fc50b8f4b38fca8b58b823cc4a3.png" alt=""></p>
<hr>
<p>这道题让我们求出逆序对个数最小值，并且允许我们随意交换一个节点的两棵子树。</p>
<p>考虑一个任意的节点，它的子树先序遍历后的逆序对显然只有三种组成：</p>
<ol>
<li>
<p>左子树中</p>
</li>
<li>
<p>右子树中</p>
</li>
<li>
<p>跨越左右子树</p>
</li>
</ol>
<p>对子树的交换显然不会影响第1,2类，因此我们只需要计算出第三类的最小值即可。</p>
<p>至于计算则没有什么难度。由于我们维护的是值域，因此左儿子必定比右儿子大，那么我们就用左儿子大小乘以右儿子大小即可得出交换前逆序对个数。交换后同理之。</p>
<p>这里需要注意，我们能够这样计算是因为无论左右儿子怎么交换，影响的都只有当前部分的逆序对个数，而不会影响深度更浅的节点的值。</p>