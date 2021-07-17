---
title: 正睿OI Day1 总结
date: 2021-06-29
tags:
 - 培训总结
 - 倍增
categories: 培训总结
description: 一些OI经典题目。
top_img: https://api.dujin.org/bing/1920.php
cover: https://tvax4.sinaimg.cn/large/0072Vf1pgy1foxlnbw4y0j31kw0w0e75.jpg
---

## RMQ

查询区间最大、最小值。

## ST表

### 预处理
设 $f[i][j]$ 表示从 $i$ 开始取 $2^j$ 个数的最小值，如果 $i$ 及其后面的数不足 $2^j$ 个则全取，形式化地说，就是下标在$[i, min(n,i+2^j-1)]$这个区间内的数的最小值。

转移：

 - $f[i][j] = min(f[i][j-1], f[i+2^{(j-1)}][j-1])$,     $j>0$ 且 $i+2^{(j-1)}\leq n$

 - $f[i][j] = f[i][j-1]$, $j>0$ 且 $i+2^{(j-1)}>n$

 - $f[i][j] = a[i]$, $j=0$

可以按照 $j$ 从小到大的顺序递推出 $f$。

### 解决询问

设当前询问为 $[l,r]$，设 x = $\lfloor{log_2(r-l+1)}\rfloor$

那么 $[l, l+2^x-1]$ 和 $[r-2^x+1,r]$ 这两个区间一定覆盖了 $[l,r]$ 这个区间内的所有元素且仅包含这个区间内的元素。 

于是答案就是 $min(f[l][x], f[r-2^x+1][x])$。
对于 $1~n$ 的每一个数预处理 $log(x)$ 下取整的值，每次询问即可做到 $O(1)$ 的时间复杂度。

### 复杂度

用 ST 表实现 RMQ 问题可以做到：

 - 时间复杂度：$O(nlogn)$ 预处理，$O(1)$ 单次查询。

 - 空间复杂度：$O(nlogn)$。

## 非递归快速幂

快速幂以前一直写递归的，今天又学了个循环的，虽然可能没什么用……

代码：

![](https://i0.hdslb.com/bfs/album/324d74baf8ffae603e4b991f10fd7e3b152be7b8.png)

## 倍增LCA

### 预处理
LCA(Lowest Common Ancestor)，即给定一棵有根树，求树上两个点的最近公共祖先的问题。

考虑维护 $an[i][j]$ 表示 $i$ 号节点的第 $2^j$ 级祖先，如果没有则为 $0$。转移即：

 - $an[i][j] = an[an[i][j-1]][j-1]$, $j > 0$

 - $an[i][j] = fa[i]$, $j = 0$

这个可以通过树上 dfs 一遍求得。

### 查询

考虑两个相同深度的点 $x,y$ 的 lca 怎么求。(假定 $x \ne y$)

容易发现它们到 lca 的距离相同，假设距离为 $d$。

对于任意的 $r\leq d$, 那么 $x$ 的 $r$ 级祖先和 $y$ 的 $r$ 级祖先相同，对于任意的 $r<d$, $x$ 的 $r$ 级祖先和 $y$ 的 $r$ 级祖先不同。

故可以按照 $k$ 从大往小的顺序，每次看两个点的第 $2^k$ 级祖先是否相同，如果相同则什么也不做，如果不同则将两个点改成它们的第 $2^k$ 级祖先。

这样做下去，直至 $k = 0$。求出的两个点一定是要求的 lca 的儿子。

考虑当 $x$ 和 $y$ 深度不同的时候怎么办？

把 $x$ 和 $y$ 的深度搞成相同的即可。

不妨假设 $dep[x]>dep[y]$，设 $x$ 的 $dep[x]-dep[y]$ 级祖先为 $z$。那么 $lca(z,y)=lca(x,y)$，$dep[z]=dep[y]$, 于是求 $lca(z,y)$ 即可。

怎么求 $z$？

考虑将 $dep[x]-dep[y]$ 拆成 $log$ 个 $2$ 的幂暴力跳即可。

时间复杂度：

 - 预处理 $O(nlogn)$，单词询问 $O(logn)$。
 - 空间复杂度：$O(nlogn)$。

## 基于 ST 表的 LCA

### 欧拉序(Euler Tour)

![](https://i0.hdslb.com/bfs/album/a1cc160fabf9e565c140a5ff5133869c51f8ba94.png)

A B D B E F E G E B A C A

就是DFS时经过点的顺序。

$N$个点的树，欧拉序长度为$2N-1$.

考虑在欧拉序中把每个点的深度写下来：

![](https://i0.hdslb.com/bfs/album/2af5b6f384dc2890a2dc045b4cbeacd1f219eba7.png)

考虑两个点的 LCA 一定是以这两个点第一次出现的位置为左右端点形成的区间内深度最小的那个点。

比如求 D 和 G 的 LCA 时 ：

![](https://i0.hdslb.com/bfs/album/f3008ea6964c4ad57006abede66c789c7a70bf58.png)

涂黄的区间里深度最小值是 $2$，故 D 和 G 的 LCA 就是对应的 B 点。

于是求 LCA 就被转化为了求 RMQ  的问题，可以使用 ST 表解决。

由于欧拉序的长度为 2n-1，故该算法的时间复杂度为：

 - 预处理 $O(nlogn)$，单次询问 $O(1)$。

 - 空间复杂度为： $O(nlogn)$。

## 二维ST表

### 预处理

考虑预处理 $f[x][y][a][b]$ 表示横坐标在 $[x, min(n, x+2^a-1)]$，纵坐标在 $[y, min(m, y+2^b-1)]$ 的矩形内的最大值。转移即：


$f[x][y][a][b] = \begin{cases}  min(f[x][y][a-1][b], f[x+2^{(a-1)}][y][a-1][b]), x+2^{(a-1)} <= n 且 a > 0
                   \\ f[x][y][a-1][b],      x+2^{(a-1)} > n 且 a > 0
                   \\ min(f[x][y][a][b-1], f[x][y+2^{(b-1)}][a][b-1]),         y+2^{(b-1)} <= m 且 b > 0
                   \\ f[x][y][a][b-1],      y+2^{(b-1)} > m 且 b > 0
                   \\ val[x][y] ,             a = 0 且 b = 0\end{cases}$

首先当其中一维是$2^0$时只转移这一维，其他时候只向另一维转移。

### 查询

对于横坐标 $[xl,xr]$，纵坐标 $[yl,yr]$ 的询问，设

则该矩形内的最大值为：

$max(f[xl][yl][kx][ky], f[xr-2^{kx}+1][yl][kx][ky], f[xl][yr-2^{ky}+1][kx][ky], f[xr-2^{kx}+1][yr-2^{ky}+1][kx][ky])$

## 一种二分答案的思路

### POJ 3419

#### Description

Mr. Flower's business is growing much faster than originally planned. He has now become the CEO of a world-famous beef corporation. However, the boss never lives a casual life because he should take charge of the subsidiary scattered all over the world. Every year, Mr. Flower needs to analyze the performance reports of these subsidiary companies.

Mr. Flower has N companies, and he numbered them with $0$ to $N – 1$. All of the companies will give Mr. Flower a report about the development each year. Among all of the tedious data, only one thing draws Mr. Flower's attention – the turnover. Turnover of a company can be represented as an integer Pi: positive one represents the amount of profit-making while negative for loss-making.

In fact, Mr. Flower will not be angry with the companies running under deficit. He thinks these companies have a large room for future development. What dissatisfy him are those companies who created the same turnover. Because in his eyes, keeping more than one companies of the same turnover is not necessary.

Now we know the annual turnover of all companies (an integer sequence Pi, the ith represents the turnover of the ith company this year.). We say a number sequence is perfect if all of its numbers are different from each other. Mr. Flower wants to know the length of the longest consecutive perfect sequence in a certain interval $[L, R]$ of the turnover sequence, can you help him?

#### Input

The first line of the input contains two integers N and M. N is the number of companies. M is the number of queries. $(1 ≤ N, M ≤ 200000)$. The second line contains N integer numbers not exceeding 106 by their absolute values. The ith of them represents the turnover of the ith company this year. The following M lines contain query descriptions, each description consists of two numbers: $L, R (0 ≤ L ≤ R ≤ N – 1)$ and represents the interval that Mr. Flower concerned.

#### Output

The output contains M lines. For each query, output the length of the longest consecutive perfect sequence between [L, R] 　

#### Sample Input
```
9 2
2 5 4 1 2 3 6 2 4
0 8
2 6
```
#### Sample Output
```
6
5
```
#### Hint

The longest perfect sequence of the first query in the sample input is '5 4 1 2 3 6', so the answer for this query is 6.

### 题解

考虑先对于每一个 $i$，求出以它为左端点，右端点最远到哪里使得这个区间内没有重复的数字。显然当左端点变大时，右端点是单调不减的，于是可以直接 two pointers 求出以每个点作为左端点时，最大的没有重复数字的区间长度，设其为 $a[i]$。

考虑一个询问 $[l,r]$，由于答案具有二分性，所以可以二分答案。判断一个答案 $x$ 是否合法时，考虑左端点在 $[l, r-x+1]$ 中，于是就只需要判断是否有在 $[l, r-x+1]$ 中的 $i$，满足 $a[i] >=x$。等价于判断 $max(a[l], a[l+1]…a[r-x+1])$ 是否 $\geq x$。该问题就是 RMQ 问题，直接使用 ST 表即可。

时间复杂度 $O(nlogn+mlogn)$。

### 什么意思呢？

首先我们观察这道题，可以发现这道题有一个特点：它是从询问的一个区间中寻找一块，并回答它的长度。而这一块本身长度又会影响选择它的合法范围。这种情况下，我们可以使用二分，假设已知长度，然后直接验证，这样是比较容易的。

## 再来一道题

### 题目

给出一棵 $n$ 个点的树，点从 $0$ 到 $n - 1$ 编号。定义一条路径的权值是路径上所有点编号的 $\operatorname{mex}$。对于每个 $0\le i\le n$ 求出 $\operatorname{mex}$ 为 $i$ 的路径有几条。注意，这里统计的路径需要包括至少一条边。

一个集合的 $\operatorname{mex}$ 定义为最小的不在集合中的非负整数。

### 题解

考虑分类讨论。

MEX = 0 的方案？

MEX = 1 的方案？

MEX > 1 的方案数？

这时这条路径一定必须经过一些点，必须不经过某一个点。

考虑必须经过的点，如果它们不在一条路径上一定无解。

故可以维护这条路径，可行的方案数就是在这条路径两端的子树内各选一个点的所有路径。

再考虑那个必须不被经过的点，分类讨论：

1.如果在路径一端的子树内。

2.如果在路径上。

3.在其它地方。

显然无解。

接下来考虑如何更新路径，即考虑新加入一个点。

1.如果在路径一端的子树内。

将这个新点的父亲那一端改为这个新点即可。

2.如果在路径上。

不做改动。

3.在其他地方。

无解，不改动，并且后面都无解（因为一个点不再同一条路径上，

考虑以 0 号节点为根写较为方便(因为路径一定会经过根节点)。

总之，就是和递推一样，逐渐将一开始只有点1的一条路径逐渐变长。然后计算路径数量即可。（两棵子树乘起来）。