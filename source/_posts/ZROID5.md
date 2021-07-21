---
title: 正睿OI Day5 总结
date: 2021-07-21
tags:
 - 培训总结
 - 字符串
categories: 培训总结
description: 正睿OI Day5总结。
top_img: https://api.dujin.org/bing/1920.php
cover: https://tvax4.sinaimg.cn/large/0072Vf1pgy1foxlhcoxbbj31kw0w0tz3.jpg
---

## 字符串哈希
### 思路
哈希( Hash )是一种神奇的查找算法，广泛运用于计算机领域，它的强大在于设计得好的哈希算法可以使对一个对象的查找时间复杂度降为 $O(1)$，这是朴素查找的 $O(n)$和二分查找的 $O(logn)$ 所远不能及的。

每一个字符都有它对应的 $\text{ASCII}$ 码，也就是说，一个字符可以表示为一个数字，将输入的字符串转换为一个 $P$ 进制数。

在这里， $P$ 是一个大数。处理完后得到的 $P$ 进制数，就叫做这个字符串的哈希值。按照我的习惯，我会取 $P=131$

### 代码

```cpp
const int P = 131;
int Hash(string tmp) {
  int hash = 0;
  for (int i = 0; i < tmp.length(); ++i) {
    hash = hash * P + tmp[i];
  }
  return hash;
}
```

### 问题

字符串的长度达到 $10^5$

所以我们还要定义一个大数 $M$ ，在 $Hash$ 函数的运算过程中令运算结果对它取模

### 代码

```cpp
const int MOD = 99991;
const int P = 131;
int Hash(string tmp) {
  int hash = 0;
  for (int i = 0; i < tmp.length(); ++i) {
    hash = (hash * P + tmp[i]) % MOD;
  }
  return hash;
}
```

## T1

### 题目

求一个字符串由多少个重复的子串连接而成。

### 思路

其实看到 “子串”，“相同的”“重复连接”这些关键词，很容易就能想到KMP算法（不会KMP可以跳p3375或自行百度）

那么KMP算法怎么在这道题实际应用呢？其实我们想到KMP的核心是在求next数组，我们不妨就利用next数组来解这道题，大家可以先想一想，如果一个字符串是由n个相同的字符串重复连接，那么它的next数组会有什么特点呢？

```
abcdabcdabcd aaaaaaaaa ababababab

000012345678 012345678 0012345678
```

写几个字符串并把它的next数组写出来，我们会发现这种字符串其实除了前面有几个0以外后面是每次加1的，那么这里面就有规律供我们解题了

我们假定这个字符串长度为n，那么$n-next[n]$（$n$减去第$n$个字符的$next$值）这个位置一定是重复的那个最长子串最后一个字符所在的位置

那么我们进一步思考，如果这整个字符串真的是由好几个相同字符串重复连接，那么$n$一定能整除$n-next[n]$，并且$\frac{n}{n-next[n]}$就是最后的答案

### 代码

```cpp
/**************************************************************
 * Problem: UVA 10298
 * Author: 芊枫
 * Date: 2021 July 21
 * Algorithm: KMP
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

const long long maxN = 90;
char str[maxN];
long long nxt[maxN];
long long LEN;

void KMP() {
  long long j = 0;
  for (int i = 2; i <= LEN; ++i) {
    while (str[j + 1] != str[i] && j) {
      j = nxt[j];
    }
    if (str[j + 1] == str[i]) {
      ++j;
    }
    nxt[i] = j;
  }
}

int main() {
  while (true) {
    scanf("%s", str + 1);
    if (str[1] == '.') {
      break;
    }
    LEN = strlen(str + 1);
    KMP();
    write(LEN / (LEN - nxt[LEN]));
    putchar('\n');
  }
  return 0;
} // Thomitics Code
```

## T2

### 题目

给出一个字符串，你有一把剪刀可以把字符串剪成两个不重复的长度为K的字符串，然后把两个拼起来

现在要你给出一种剪法使得拼接之后的字符串包含另一个新的字符串

$字符串长度\leq 5*10^5$

### 思路

1、如果小串长度小于分割长度，则先匹配小串是否在大串中出现，如果出现直接切割为一边包含小串的

2、如果没出现，那就一定要分割小串，枚举分割，最左匹配（从左往右匹配），最右匹配（从右往左匹配），如果这两个匹配的字符串有交集或是根本无法匹配则一定不可以

左侧匹配单调不减，右侧匹配单调不增。

<div class='tip'><p>如果用哈希，可以使用一种类似于莫队的计算哈希的方式。就是向右移动时，去掉前面出去的那一位对哈希的影响，加上后面进入滑块的那一位对哈希的影响。</p></div>

但是其实完全用不着哈希，我们使用KMP进行匹配，复杂度是一样的。

## T3

### 题目

给了一个母串S, 每次循环给了一个模板串，问模板串在母串中“匹配”了多少次？

“匹配”的意思就是首字母和尾字母一样， 中间字母顺序可以换

询问次数 $20000$

保证询问的字符串总长度不超过 $100000$

$S$ 长度不超过 $100000$


### 思路

其实挺好想到的，可以哈希首位、末尾和各字母出现次数。然后我们对于长度相同的模板串，可以一起去哈希，比较。

<div class='tip'><p>对于这种不用考虑某一种特性的题目，我们可以考虑找到一种忽略这种特性的哈希方案来做，比如说这个题就可以只对出现次数进行哈希。</p></div>

考虑如果所有串长度都不同，没法一起比较，复杂度会不会很高？其实不会的。最坏情况，如果所有串长度都不一样，是一个首相为1，公差为1的等差数列，则串个数为根号级别。

## T4

### 题目

给定一个字符串

$200000$ 次询问

每次询问两个子串是不是同构

同构：要求对$S$中某一特定字母，总能找到在T中的某一字母与其字母出现个数相同，且每个出现字母对应的位置相同

例如 `aaabb` 和 `cccdd` 同构

### 思路

区间哈希 $hash[l,r]=hash[r]-hash[l]*p^{r-l}$

对于每一个字母维护再每个位置上是否出现（01串）

然后排序，哈希

## 二维哈希

### 思路

找出某个 x × y 的矩阵在某个 n × m 的矩阵中的所有出现位置。

首先对于每个位置，求出 它开始长度为 y 的横行的 hash 值

然后对于 hash 值再求一次竖列的 hash 值即可

简单来说，对于每个长度为小块做一次哈希，然后竖向对哈希值做哈希

### 代码

![](https://i0.hdslb.com/bfs/album/ab68865f389a320f19f27eb7d1e16c0b799642cd.png)

## Trie

### 简介

字典树，又称为单词查找树，Tire数，是一种树形结构，它是一种哈希树的变种。

![](https://i0.hdslb.com/bfs/album/70b145cdb44135b83f8482b562c0de6842b864c7.png)

### 性质

根节点不包含字符，除根节点外的每一个子节点都包含一个字符
从根节点到某一节点。路径上经过的字符连接起来，就是该节点对应的字符串
每个节点的所有子节点包含的字符都不相同

### 代码

```cpp
struct Trie
{
    int ch[400010][26],val[400010];
    int sz;
    void init()
    {
        sz=1;
        memset(ch[0],0,sizeof(ch[0]));
    }
    int idx(char c){return c-'a';}
    void insert(char *s,int v)
    {
        int u=0,n=strlen(s);
        for (int i=0;i<n;i++)
        {
            int c=idx(s[i]);
            if (!ch[u][c])
            {
                memset(ch[sz],0,sizeof(ch[sz]));
                val[sz]=0;
                ch[u][c]=sz++;
            }
            u=ch[u][c];
        }              
        val[u]=v;
    }
}tree;
```

## T5 

### 题目
给定一字符串，以及一个字典（字符串的set）
问将字符串拆成字典中的单词（不能相交）有多少种拆法
字符串长度不超过 300000
字典单词不超过 4000个，每个单词长度不超过 100

### 思路

考虑 Trie+DP。

我们先来看DP：

DP方程的状态：$f[i]$表示$s[i..len]$的串有多少种表示方法

DP方程的转移：$f[i]=∑(f[j+1])$,且要满足$s[i..j+1]$可以由多个字典拼成，而判断这个就可以用到高效的$trie$字典树。

DP的结果很显然就是$f[0]$,因为$i$是$len->1$枚举的

### 代码

```cpp
/**************************************************************
 * Problem: OJ ProID
 * Author: 芊枫
 * Date: 2021 Mon Date
 * Algorithm:
 **************************************************************/

#include <algorithm>
#include <asm-generic/errno.h>
#include <bits/stdc++.h>
#include <cmath>
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

const long long maxN = 300090;
char totSTR[maxN];
long long totN;
string strs;
char ch[maxN][30];
long long cntNODE;
bool flag[maxN];
long long DP[maxN];
const long long MOD = 20071027;
long long cas;

void insert(string tmp) {
  long long now = 0;
  int nowC = 0;
  for (int i = 0; i < tmp.length(); ++i) {
    nowC = tmp[i] - 'a' + 1;
    if (!ch[now][nowC]) {
      ++cntNODE;
      ch[now][nowC] = cntNODE;
    }
    now = ch[now][nowC];
  }
  flag[now] = 1;
}

int main() {
  while (~scanf("%s", totSTR)) {
    memset(ch, 0, sizeof(ch));
    memset(flag, 0, sizeof(flag));
    totN = read();
    for (int i = 1; i <= totN; ++i) {
      cin >> strs;
      insert(strs);
    }
    memset(DP, 0, sizeof(DP));
    long long len = strlen(totSTR);
    DP[len] = 1;
    for (int i = len - 1; i >= 0; --i) {
      int x = 0;
      for (int j = i; j < len; ++j) {
        int yy = totSTR[j] - 'a' + 1;
        if (!ch[x][yy]) {
          break;
        }
        x = ch[x][yy];
        if (flag[x]) {
          DP[i] = (DP[j + 1] + DP[j] + MOD) % MOD;
        }
      }
    }
    printf("Case %lld: %lld\n", ++cas, DP[0]);
  }
  return 0;
} // Thomitics Code
```

## T6

### 题目
给出 n 个数，找到一对数，他们的 xor 是最大的。

个数 <=10^5

数 <2^32

### 思路

考虑对于一个二进制数，只要前面的一位为0，后面的再怎么样，也一定比前面都一样，这一位为1的数字小。与字典序相似，贪心尽量选择不同的。


## T7

### 题目

给出完整的字典单词列表

新词的构词规则：认可的词必须是字典词或由两部分组成，其中第一部分必须是字典词或其非空前缀，第二部分是字典词或其非空后缀

求有多少本质不同的单词

字典单词<=10000

单词长度<=40

### 思路
建立一个前缀树，一个后缀树，在两个树上面分别匹配，然后为了不重复（就是有的这一截算是前缀中的，有的这一截算是后缀中的，但是实际上他们相同），要尽量多的使用前缀，也就是使用极长前缀（不能再长了，再长就不是前缀了），然后统计数量。

注意到有一种特殊情况：

比如说有一个单词是`back`，还有一个单词是`c`，组成的单词`bac`理应是合法的，但是因为我们要使用极长前缀，这个`bac`会被判定为`back`的前缀，后缀为空，不合法，然后不统计，但实际上是合法的，应该统计。所以我们应该特别判断这种一个单词是某个前缀的后缀，特别统计即可。

## AC自动机

### 简介

一种字符串的快速匹配算法。适用于多模式串的匹配。
AC自动机是Trie加上失配边组成的。

所谓失配边，即是在当前位置向下匹配的时候失败了，把正在匹配的原串向后至少移动多少位置，可以使原串的前缀和当前模式串的某一段匹配。

所以失配边指向的是接下来可以继续向下匹配的位置，一定是由深度深的点向深度欠的点指的。

### 例图

![](https://i0.hdslb.com/bfs/album/3ed64f0a660d27ad02d3574f0a4b8cee02da69a5.png)

### 构造Fail边

 - 假设当前节点为temp

 - 我们找到temp的父节点，得到父节点的fail节点
再找到fail节点的所有子节点

 - 寻找子节点中是否有和temp节点值相同的节点

   - 若存在则temp的fail节点指向该节点

   - 如不存在继续寻找该fail节点的fail节点

   - 直到找到与相同值的节点或达到root节点。

### 代码

```cpp
const int SIGMA_SIZE = 26;
const int MAXNODE = 5000010;
const int MAXS = 200 + 10;
struct AhoCorasickAutomata
{
    int ch[MAXNODE][SIGMA_SIZE];
    int f[MAXNODE];    // fail函数
    int val[MAXNODE];  // 每个字符串的结尾结点都有一个非0的val
    int last[MAXNODE]; // fail链上的下一个单词结尾结点
    int cnt[MAXS]; //用来统计模式串被找到了几次
    int sz;
    void init()
    {
        sz = 1;
        memset(ch[0], 0, sizeof(ch[0]));
        memset(cnt, 0, sizeof(cnt));
    }
    int idx(char c){return c-'A';}
    // 插入字符串。v必须非0
    void insert(char *s, int v)
    {
        int u = 0, n = strlen(s);
        for(int i = 0; i < n; i++)
        {
            int c = idx(s[i]);
            if(!ch[u][c])
            {
                memset(ch[sz], 0, sizeof(ch[sz]));
                val[sz] = 0;
                ch[u][c] = sz++;
            }
            u = ch[u][c];
        }
        val[u] = v;
    }
    // 递归打印以结点j结尾的所有字符串
    void print(int j)
    {
        if(j)
        {
            cnt[val[j]]++;
            print(last[j]);
        }
    }
    // 在T中找模板
    void find(char* T)
    {
        int n = strlen(T);
        int j = 0; // 当前结点编号，初始为根结点
        for(int i = 0; i < n; i++)
        {
            int c = idx(T[i]);
            j = ch[j][c];
            if(val[j]) print(j);
            else if(last[j]) print(last[j]); // 找到了！
        }
    }
    // 计算fail函数
    void getFail()
    {
        queue<int> q;
        f[0] = 0;
        // 初始化队列
        for(int c = 0; c < SIGMA_SIZE; c++)
        {
            int u = ch[0][c];
            if(u) { f[u] = 0; q.push(u); last[u] = 0; }
        }
        // 按BFS顺序计算fail
        while(!q.empty())
        {
            int r = q.front(); q.pop();
            for(int c = 0; c < SIGMA_SIZE; c++)
            {
                int u = ch[r][c];
                if(!u) {ch[r][c] = ch[f[r]][c];continue;}
                q.push(u);
                int v = f[r];
                while(v && !ch[v][c]) v = f[v];
                f[u] = ch[v][c];
                last[u] = val[f[u]] ? f[u] : last[f[u]];
            }
        }
    }
}ac;
```

### 特性

1. 和Tire不同的是，Trie的结点 x如果满足 val[x]>0那么表示从根到 x是一个单词。
2. 但是AC自动机里面，每一个结点不再仅仅是一个单词的结尾，它还包含了所有以他为结尾作为前缀的串。
比如上图的 5结点，他既表示 she ，也表示he。
如果 she 继续匹配失败了，那么 he 仍然是成立的。所以 5结点除了记录是 she 的结尾，也应该记录，当从 5走失配边的时候，下一个单词是什么，这个例子里，就是 he。
1. 我们用 last[x]表示从 x走失配边的时候，遇到下一个单词结点的编号，这个专业名次叫做后缀链接（suffix link）。