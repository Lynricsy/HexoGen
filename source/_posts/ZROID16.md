---
title: 正睿OI Day16 总结
date: 2021-07-31
tags:
 - 培训总结
 - 2-SAT
categories: 培训总结
description: 正睿OI Day16总结。
top_img: https://api.dujin.org/bing/1920.php
cover: https://tvax4.sinaimg.cn/large/0072Vf1pgy1foxk3oqnw0j31hc0u0qnm.jpg
---

## 拓扑序

### 定义

给定一个有向图，求出一个点的序列，使得对于每条边 𝑢→𝑣 都满足 u 在 v 的前面出现。

### 定理

 - 有环的图一定没有拓扑序。
 - 对有向无环图，一定有拓扑序。

### 如何计算？

每次选一个入度为 0 的点，加到拓扑序末尾，然后在原图里删掉这个点和它的所有出边。

### 伪代码

```
addedge(u, v): 
    deg[v]++

queue Q

for i in graph:
    if deg[i] = 0:
        Q.push(i);
while Q not empty:
    u = Q.front();
    Q.pop();
    a[++cnt] = u;
    for (u -> v) in graph:
        --deg[v];
        if deg[v] = 0:
            Q.push(v);

```

### 判断环

算法结束时，如果还有点没被删除，说明图中有环。

## T1

### 题目

给定一个 n 个点混合图，有 m 条有向边和 k 条无向边。给无向边定向，使它成为 DAG，或判断无解。

### 思路

看到这个题目的“没有坏”，比较容易想到跟拓扑序有关系。想到拓扑序的性质——一张图，如若有拓扑序，则它一定没有环。

所以我们可以先将原图有向边的部分进行拓扑排序。显然如果原图已经有环，则一定不行。

然后将原图拓扑排序，按照拓扑排序完的结果，无向边从拓扑序小的连向拓扑序大的即可。

## DAG最长链

### 题目

给定一个 DAG，每个点有权值（可以是负数），问选一条链，权值和最大是多少。

### 思路

其实非常显然。

拓扑排序+DP即可。

dp[x] 表示 x 开始的最长链长度。

dp[x] = max(dp[y]) + w[x] (x 到 y 有边)

转移顺序：拓扑序从后往前，也就是从“边缘”点向”中心“点。

## 传递闭包

### 题目

给定一个 n 个点 m 条边的 DAG，对每个点求出它能到达多少点。

$n, m \leq 10^5$


### 思路

相当于是一种状压的思想。

但是很显然，这个题的数据范围，不可能使用普通的数据类型状压。很长的01串——对，就是`bitset`！

![](https://cdn.jsdelivr.net/gh/Thomitics/PicBed@master/pics/20210801202224.png)

## Reboot from Blue

### 题目

数轴上有 $n$ 个加油站，第 $i$ 个位于 $x_i$，油价是每升 $c_i$ 元。

YSGH 和他的车一开始位于坐标 $s$，它想去坐标 $t$（$s < t$），车的油箱一开始是空的，保证坐标 $s$ 有加油站。

假设车的油箱容量无限大，一升油能走距离 $1$。

他想知道他需要花费的最小费用。

### 思路

这个题好像非常之经典……解法非常多。

首先我们可以想一下，最优策略显然是每次刚好跑到一个油钱更小的加油站

单调栈求左右两边第一个油钱更小的加油站，连边连出一个 DAG，跑 s 到所有点的最短路，用它更新答案

不用跑最短路，直接跑 dp

## 最小拓扑序

### 题目

求 DAG 拓扑序中字典序最小的

### 思路

非常简单，与在`Trie`树上面寻找一个字典序最小的字符串一样，贪心选择即可。

用小根堆存入度为 0 的点，每次取出最小的

## 树的拓扑序个数

### 题目

给定一棵外向树，问拓扑序个数

### 思路

给每个点 i 一个标号 𝑝_𝑖，表示它在拓扑序中出现的位置。
对每条边 𝑢→𝑣，都要满足 𝑝_𝑢<𝑝_𝑣 。

dfs(x, S): 决定 x 子树内的标号，每个点的标号在集合 S 中取。
先给 x 标上 S 中的最小元素，并在 S 中删除这个元素。
再将 S 分配给每个儿子的子树，递归调用 dfs(y, S’)。

## 拓扑序计数

### 题目

给定 $n$ 个点 $m$ 条边的有向图，每条边可能出现/不出现，对所有 $2^m$ 种情况，求图中拓扑序个数的和。

### 思路


 - 原来是先枚举删边方案，累加拓扑序个数
 - 
 - 现在先枚举拓扑序，累加合法原图的数量
 - 
 - 设有 k 条边满足 u 在 v 之前，排列的权值为 2^k
 - 
 - dp[msk] 表示排列里已经有 msk 的点，排列的权值和

## 牛 x 的点

### 题目

给定一个 n 个点 m 条边的 DAG，对每个点 x，判断它是否满足：对于图中任意一点 y，要么从 x 能到 y，要么从 y 能到 x

### 思路

#### 总体

 - 先跑拓扑排序
 - 条件等价于：
   - 若 y 拓扑序在 x 之前，那么 y 能到 x
   - 若 y 拓扑序在 x 之后，那么 x 能到 y
 - 两个条件分别判，最后取 and
  
#### 条件一咋判？

假设 x 前的一段区间符合
`? ? y [? ? ? x]`
找到 y 的后继中拓扑序最小的点 $f(y) = z$
（拓扑序最小的 z 满足存在边 y -> z）
`ok:「? ? y [? z ? x] ? ? ?」`
`ok:「? ? y [? ? ? z(x)] ? ? ?」`
`not ok:「? ? y [? ? ? x] ? z ?」`

所有拓扑序比 x 小的 y，都满足 $f(y)$ 拓扑序不比 x 大

## DAG 重链剖分

### 题目

![](https://cdn.jsdelivr.net/gh/Thomitics/PicBed@master/pics/20210801231034.png)

### 思路

建 DAG，点 i 的出边 $to[i][ch]$ 连向 i 之后第一个字符 ch 的位置

本质不同子序列个数 = DAG 从点 $0$ 出发的路径数

记点 i 出发的路径数为 $f(i)$，若 $> 10^18$ 设为 $inf$

对点 i，若 $f(i) != inf$，选择 $f(j)$ 最大的作为重边

否则有些字母走不到，选出最大的可能走到的字母，把后面的出边删掉，把重边设成最大字母的出边

从 0 出发，每走一条轻边 $f$ 的值就会减半（或从 $inf$ 变为非 $inf$），只会减 $log$ 次

在重链上二分走出去的位置（用倍增实现）
$O(n log + Q log^2)$

## 2-SAT

### 处理的问题

![](https://cdn.jsdelivr.net/gh/Thomitics/PicBed@master/pics/20210801232938.png)

### 思路

建图，对每个变量建 F(i) 和 T(i) 两个点，表示它取 0 或 1。

目标：每个 i 选 T, F 中的一个点

一个条件拆成两个：

 - 若 𝑦_𝑢=0，则 𝑦_𝑣=1
 
 - 若 𝑦_𝑣=0，则 𝑦_𝑢=1

𝑦_𝑖 取一个值，相当于选图中一个点

 - 从 𝑦_𝑢=0 对应的点到 𝑦_𝑣=1 对应的点连一条有向边
  
 - 从 𝑦_𝑣=0 对应的点到 𝑦_𝑢=1 对应的点连一条有向边

若 T(i), F(i) 处于同一强联通分量，一定无解

跑 tarjan 判断

强联通分量缩点，跑拓扑排序

选 T, F 中拓扑序较大的点

根据昨天提到的定理，可以想到简便方法：取 tarjan 完所属 scc 标号较小的点。

连边：

![](https://cdn.jsdelivr.net/gh/Thomitics/PicBed@master/pics/20210801233558.png)

### 提示

一些问题看着像 2-SAT，实际上不能做

 - 最大 1 个数
 - 1 个数能否超过一半
 - 其它的一些东西

尝试其他思路。

理智判断，不要在考场上拿图灵奖

## CF1215F Radio Stations

### 题目

有 $p$ 种电站，需要选择一种电站序列使其满足：

- $n$ 对 $(x,y)$，$x$ 电站或 $y$ 电站中至少有一个
- $m$ 对 $(x,y)$，$x$ 电站或 $y$ 电站中最多有一个



- 第 $i$ 种电站有两个参数 $li$ 和 $ri$ $(1\leq li\leq ri\leq M)$，表示其覆盖的区间为$[li,ri]$。若选定一个电站序列，序列中所有电站覆盖区间的交集不能为空。要求若求得一种合法电站序列，输出交集中任意一个点。

### 思路

其余 k 个限制就是 2-SAT，关于 f 的限制不好做

等价于：若电台 i, j 区间不交，不能同时选

从 T(i) 连出的边，终点有两种：
 - r(j) < l(i) 的 F(j)
 - l(j) > r(i) 的 F(j)
  
前缀/后缀和优化建边即可。

## 编码

### 题目

有 n 个二进制串，每个串有一位是 `?`

你要给 ? 填上 0/1，使得不存在 i, j 使 s[i] 是 s[j] 的前缀


## Metal Processing Plant

### 题目

Yulia works for a metal processing plant in Ekaterinburg. This plant processes ores mined in the Ural mountains, extracting precious metals such as chalcopyrite, platinum and gold from the ores. Every month the plant receives $n$ shipments of unprocessed ore. Yulia needs to partition these shipments into two groups based on their similarity. Then, each group is sent to one of two ore processing buildings of the plant.

To perform this partitioning, Yulia first calculates a numeric distance $d(i, j)$ for each pair of shipments $1 \le i \le n$ and $1 \le j \le n$, where the smaller the distance, the more similar the shipments $i$ and $j$ are. For a subset $S \subseteq \{ 1, \ldots , n\} $ of shipments, she then defines the disparity $D$ of $S$ as the maximum distance between a pair of shipments in the subset, that is,

$$ D(S) = \max _{i, j \in S} d(i, j). $$

Yulia then partitions the shipments into two subsets $A$ and $B$ in such a way that the sum of their disparities $D(A) + D(B)$ is minimized. Your task is to help her find this partitioning.

### 思路

枚举 a, b，其中 a, b 都是出现过的边权

判断 $max(L) \leq a, max(R) \leq b$ 是否可行 

点属于 L 还是属于 R 看作 bool 变量

 - 若边权 > a，那么至少一个端点在 R
 - 若边权 > b，那么至少一个端点在 L

a 变小时，最小的 b 一定变大

two-pointers，$O(n^2)$ 次 2-SAT

2-SAT 时间：$O(n^2)$

bitset 优化：$O(n^2/w)$

## 子树2-SAT

### 问题

考虑的问题加了一个词条

给定一棵二叉树，叶子是 m 条限制。对每个子树问是否有解。

### 思路

合法称答案为 1，否则为 0

对每个链顶做一次，只要对为 0 的链二分

剪枝：若链上某点存在轻儿子为 0，那么它上面的点也是 0

找到链上最深的存在轻儿子为 0 的点，标记链上它的后继

二分区间是 [标记点，链底]

重链剖分，链顶 size 和是 $O(n log n)$

对二分的部分，二分一次复杂度不超过 $O(标记点 size)$

标记点没有祖先-后代关系，size 和不超过 $n$

二分不超过 log 层，复杂度 $O(n log n)$
