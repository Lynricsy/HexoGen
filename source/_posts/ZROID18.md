---
title: ZROID18
date: 2021-02-09
tags:
 - Draft
categories: 生物
description: 生物必修一细胞呼吸与ATP，光合作用整理。
top_img: https://api.dujin.org/bing/1920.php
cover: https://tvax3.sinaimg.cn/large/0075LAc9ly1h22k3bji1rj30le183av8.jpg
---
## T1

### 题目

你今天被分配到了一个任务：在你的面前有一个两排的架子。摆放和移动杠铃的规则如下：

 - 在一排内部，每个整数坐标处都可以放一个杠铃，一个坐标上最多只能同时放下一个杠铃。
 - 初始时，两排架子上各自的 1 ∼ n 位置上都各放了一个杠铃。
 - 你可以将一个杠铃往左或往右移动一个单位，但需要满足移动到的位置在这之前是空的。这个操作可以对任意多个杠铃进行任意多次，且不耗费力气。
 - 你可以将一个杠铃移动到任意一排的任意一个此前为空的位置（整数坐标处）。这个操作可以进行多次，每次耗费的力气等于移动的杠铃的质量。
  
刚开始架子上摆放着 n 对共 2n 个杠铃，其中每对杠铃拥有相同且唯一的质量，即保证没有两对杠铃的质量相等。你的目标是进行若干次操作，使得每一对的两个杠铃都被放在了同一排架子的相邻两个位置上，不需要考虑不同对之间的位置关系。

你需要计算出，在达到目标的前提下，耗费的力气最大的操作最小可以是多少？

### 思路

最大值最小化，很容易想到是二分答案。难点就在判断答案是否合理。

首先对于$\leq Mid$ 的点，容易想到这样的点可以任意移动，对答案不造成影响。所以他们其实可以被忽略不计。有影响的就是$>Mid$ 的点。这种点无法在两行间移动。如果有两个这样的点不在同一行上面，或是有几个不同质量的这种点穿插，则无法符合要求。根据这个写`check()`函数即可。

### 代码

```cpp
/**************************************************************
 * Problem: OJ ProID
 * Author: 芊枫
 * Date: 2021 Mon Date
 * Algorithm:
 **************************************************************/
#include <bits/stdc++.h>
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
long long A[maxN];
long long B[maxN];
long long L, R;
long long Mid;

bool check(long long nowX) {
  long long last = 0;
  for (int i = 1; i <= totN; ++i) {
    if (A[i] <= Mid) {
      continue;
    }
    if (!last) {
      last = i;
    } else if (last && A[i] != A[last]) {
      return false;
    } else {
      return 0;
    }
  }
  if (last) {
    return false;
  }
  last = 0;
  for (int i = 1; i <= totN; ++i) {
    if (B[i] <= Mid) {
      continue;
    }
    if (!last) {
      last = i;
    } else if (last && B[i] != B[last]) {
      return false;
    } else {
      return 0;
    }
  }
  if (last) {
    return false;
  }
  return true;
}

long long totANS;

int main() {
  totN = read();
  for (int i = 1; i <= totN; ++i) {
    A[i] = read();
  }
  for (int i = 1; i <= totN; ++i) {
    B[i] = read();
  }
  L = 0;
  R = 1E9 + 223;
  while (L <= R) {
    Mid = (L + R) >> 1;
    if (check(Mid)) {
      totANS = Mid;
      R = Mid - 1;
    } else {
      L = Mid + 1;
    }
  }

  write(Mid);
  return 0;
} // Thomitics Code
```

## T2

### 题目

你有一个长度为 $n$ 的序列 $a$，你需要对其中的每个数进行恰好一次操作，使得序列中的所有元素值相等。

一个操作将元素的值从 $a_i$ 修改为 $a_i'$ 的代价为 $(ai − a_i')^2$。

你想要让所有操作的代价之和尽可能小。

### 思路

头一次看见题目名称叫“简单题”还真就是道简单题的。

没什么思路，就是一个个枚举要变成的值即可。

然后就是要注意把式子拆开，预处理一下，快速求值。这样可以做到O(1)求值，O(n)枚举。

### 代码

```cpp
/**************************************************************
 * Problem: OJ ProID
 * Author: 芊枫
 * Date: 2021 Mon Date
 * Algorithm:
 **************************************************************/
#include <bits/stdc++.h>
#include <map>
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
long long MAXX = -INF;
long long MINN = INF;
long long SUM;
long long nowANS;
long long A[maxN];
long long totANS = 0x7f7f7f7f7f7f7f7f;
long long SUMP;

int main() {
  totN = read();
  for (int i = 1; i <= totN; ++i) {
    A[i] = read();
    SUM += A[i];
    SUMP += A[i] * A[i];
    MAXX = max(MAXX, A[i]);
    MINN = min(MINN, A[i]);
  }
  for (long long i = MINN; i <= MAXX; ++i) {
    nowANS = totN * i * i - 2 * SUM * i + SUMP;
    totANS = min(totANS, nowANS);
  }
  write(totANS);
  return 0;
} // Thomitics Code
```

## T3

### 题目

![](https://cdn.jsdelivr.net/gh/Thomitics/PicBed@master/pics/20210803194744.png)

### 思路

令 $f_i$ 表示以 $i$ 结尾的前缀的答案。枚举 $j<i$，如果 $s_{j+1~i}$ 是好的字符串,那么就用 $f_j+1$ 更新 $f_i$。

预处理出可行的 $j$ 的区间。

这个问题就变成了找出$f_{j+1~i}$中好的字符串最大值，来更新$f_i$。单调队列维护即可。

### 代码

```cpp
#include <bits/stdc++.h>
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
long long LEN;
long long totN;
long long vis[39];
long long DP[maxN];
char str[maxN];

int main(void) {
  totN = read();
  scanf("%s", str + 1);
  LEN = strlen(str + 1);
  for (int i = 1; i <= LEN; i++) {
    DP[i] = INF;
  }
  DP[0] = 0;
  for (int i = 1; i <= LEN; i++) {
    long long cnt = 0;
    memset(vis, 0, sizeof(vis));
    for (int j = i; j >= 1; j--) {
      if (!vis[str[j] - 'a']) {
        cnt++;
      }
      vis[str[j] - 'a']++;
      if (cnt == totN) {
        DP[i] = std::min(DP[i], DP[j - 1] + 1);
      }
      if (cnt > totN) {
        break;
      }
    }
  }
  for (int i = 1; i <= LEN; i++) {
    if (DP[i] == INF) {
      write(-1);
      putchar('\n');
    } else {
      write(DP[i]);
      putchar('\n');
    }
  }
  return 0;
} // Thomitics Code
```

## T4

### 题目

今天是你开学的日子，被分配到的新班级当中大家互不认识。聪明的你想出了一个方法帮助大家方便迅速地找到合适的朋友。

你挑选了 $m$ 个问题抛给所有的同学，每个问题的答案都可以用是或否表示，比如：你是否喜欢数学、你是否来自地球等。每位同学按照顺序如实回答这 $m$ 个问题，并将得到的每一个答案从左到右排列起来，对于一个问题，如果答案是肯定的，那么写下一个字符$"1"$（不包含引号），否则写下一个字符$"0"$（不包含引号）。这样，每位同学都拥有了一个长度为 $m$ 的 $01$ 串。

你认为，两个同学对问题的相同答案越多，他们之间的相似度就越大。因此，我们定义两个同学之间的相似度为二人各自的 $01$ 串中相同的位置数量。

你的班级当中一共有 $n$ 个同学，现在剩下的同学都已经写好并得到了他们自己的 $01$ 串，你决定搞点事情。

你希望自己是不同于其他同学十分独特的存在，也就是说，你希望和你相似度最大的同学与你的相似度尽可能地小。现在你得到了其他所有同学的 $01$ 串，你想知道如果你能够任意地回答每个问题，也就是任意地确定自己的 $01$ 串，这个最小的相似度是多少，以及有多少种可能的回答方案能够达到这个最小的相似度。

### 思路

把这些01串看成一个图，使得距离越远，相似程度越小。

其实很容易构建这样的图，构造成一个多维的网格图，所有输入的字符串都是起点，每个点和自己差一位的点连边。

找出距离最大的点。

直接多源$BFS$即可。

### 代码

```cpp
/**************************************************************
 * Problem: OJ ProID
 * Author: 芊枫
 * Date: 2021 Mon Date
 * Algorithm:
 **************************************************************/
#include <bits/stdc++.h>
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

const long long maxN = 300090;
long long totN, totM;
long long totANS, SUM;
int DP[maxN];
bool vis[maxN];
char str[39];
queue<int> Q;

int main() {
  totN = read();
  totM = read();
  for (int i = 1; i <= totN - 1; i++) {
    scanf("%s", str + 1);
    int summ = 0;
    for (int j = 1; j <= totM; j++) {
      summ = summ * 2 + (str[j] - '0');
    }
    if (!vis[summ]) {
      vis[summ] = 1;
      Q.push(summ);
      DP[summ] = 0;
    }
  }
  while (!Q.empty()) {
    int x = Q.front();
    Q.pop();
    for (int i = 0; i < totM; i++) {
      int y = x ^ (1 << i);
      if (vis[y])
        continue;
      vis[y] = 1;
      DP[y] = DP[x] + 1;
      Q.push(y);
      if (DP[y] > totANS) {
        totANS = DP[y];
        SUM = 1;
      } else if (DP[y] == totANS) {
        SUM++;
      }
    }
  }
  write(totM - totANS);
  putchar(' ');
  write(SUM);
  return 0;
}
```

## T5

### 题目

你有一个 $n × m$ 的棋盘。

你现在手上有若干个棋子，想要把它们放到棋盘里。不过，你需要保证任意一行任意一列最多**只有三个棋子**。

现在你想要知道有多少种本质不同的摆放棋子的方案。两种方案本质不同当且仅当一个方案里某个位置有棋子而另一个方案里没有。

### 思路

显然我们对具体哪些行是放了 y 个，R 个，k 个还是 j 个并不感兴
趣。我们感兴趣的只是这样放的行的个数。

$f[i][a][b][c]$ 表示现在考虑到了第 $i$ 列，已经有 $a$ 行放了 $0$ 个, $b$ 行放了 $1$ 个,$c$ 行放了 $2$ 个。（那么显然有 $n-a-b-c$ 行放了 $3$ 个）

每次转移的时候枚举这一列的几个棋子分别放到了之前有多少个棋
子的行里。

用 DFS 来转移即可。
