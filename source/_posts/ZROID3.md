---
title: 正睿OI Day3 总结
date: 2021-07-19
tags:
 - 培训总结
categories: 培训总结
description: 正睿OI Day3总结。
top_img: https://api.dujin.org/bing/1920.php
cover: https://tvax4.sinaimg.cn/large/0072Vf1pgy1foxlnf6ksdj31kw0w0dzb.jpg
---

## T1

### 题目

![](https://i0.hdslb.com/bfs/album/3874e0394c0df475b2684fccf7138aa147c17fd7.png)

### 题解

emm……首先这个题易发现对于一个字典序，只有每一次都走最小的才行，因为只要前面一个大了，后面的再小也没用。难点就在于如果当前这个格子右面和下面相等该怎么办。

考虑反向解决。用$f[i][j]$表示$(i,j)$走到$(n,m)$最小的字典序字符串。

### 考场代码（一行两行没处理好，48分）

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

const long long maxN = 2090;
long long totN, totM;
char mapp[maxN][maxN];
string totANS;
struct Node {
  string str;
  long long x, y;
};
bool operator==(Node a, Node b) {
  return a.str == b.str && a.x == b.x && a.y == b.y;
}

Node DFS(char nowC, long long nowX, long long nowY, string nowSTR) {
  if (nowX >= totN && nowY >= totM) {
    cout << nowSTR;
    exit(0);
    return {nowSTR, nowX, nowY};
  }
  if (nowX == totN) {
    return {nowSTR + mapp[nowX][nowY + 1], nowX, nowY + 1};
  }
  if (nowY == totM) {
    return {nowSTR + mapp[nowX + 1][nowY], nowX + 1, nowY};
  }
  if (mapp[nowX + 1][nowY] < mapp[nowX][nowY + 1] && mapp[nowX + 1][nowY]) {
    return {nowSTR + mapp[nowX + 1][nowY], nowX + 1, nowY};

  } else if (mapp[nowX + 1][nowY] > mapp[nowX][nowY + 1] &&
             mapp[nowX][nowY + 1]) {
    return {nowSTR + mapp[nowX][nowY + 1], nowX, nowY + 1};
  } else {
    Node a, b;
    a = DFS(mapp[nowX + 1][nowY], nowX + 1, nowY,
            nowSTR + mapp[nowX + 1][nowY]);
    b = DFS(mapp[nowX][nowY + 1], nowX, nowY + 1,
            nowSTR + mapp[nowX][nowY + 1]);
    while (a.str == b.str) {
      nowSTR = a.str;
      a = DFS(mapp[a.x][a.y], a.x, a.y, nowSTR);
      b = DFS(mapp[b.x][b.y], b.x, b.y, nowSTR);
    }
    if (a.str < b.str) {
      return a;
    } else if (a.str > b.str) {
      return b;
    } else {
      return a;
    }
  }
}

void SH() {
  long long nowX = 1;
  long long nowY = 1;
  while (true) {
    if (nowX == totN && nowY == totM) {
      cout << totANS;
      exit(0);
      return;
    }
    if (nowX == totN) {
      totANS = totANS + mapp[nowX][nowY + 1];
      ++nowY;
    } else if (nowY == totM) {
      totANS = totANS + mapp[nowX + 1][nowY];
      ++nowX;
    } else if (mapp[nowX + 1][nowY] < mapp[nowX][nowY + 1] &&
               mapp[nowX + 1][nowY]) {
      totANS = totANS + mapp[nowX + 1][nowY];
      ++nowX;
    } else if (mapp[nowX + 1][nowY] > mapp[nowX][nowY + 1] &&
               mapp[nowX][nowY + 1]) {
      totANS = totANS + mapp[nowX][nowY + 1];
      ++nowY;
    } else {
      Node a, b;
      a = DFS(mapp[nowX + 1][nowY], nowX + 1, nowY,
              totANS + mapp[nowX + 1][nowY]);
      b = DFS(mapp[nowX][nowY + 1], nowX, nowY + 1,
              totANS + mapp[nowX][nowY + 1]);
      if (a.str < b.str) {
        totANS = a.str;
        nowX = a.x;
        nowY = a.y;
      } else if (a.str > b.str) {
        totANS = b.str;
        nowX = a.x;
        nowY = a.y;
      } else {
        totANS = a.str;
        return;
      }
    }
  }
}

int main() {
  totN = read();
  totM = read();
  for (int i = 1; i <= totN; ++i) {
    scanf("%s", mapp[i] + 1);
  }
  putchar(mapp[1][1]);
  SH();
  return 0;
} // Thomitics Code
```

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

const long long maxN = 2090;
long long totN, totM;
bool flag[maxN][maxN];
char mapp[maxN][maxN];

int main() {
  totN = read();
  totM = read();
  for (int i = 1; i <= totN; ++i) {
    scanf("%s", mapp[i] + 1);
  }
  putchar(mapp[1][1]);
  flag[1][1] = true;
  for (int i = 3; i <= totN + totM; ++i) {
    char MIN = 'z' + 1;
    for (int j = max(1ll, i - totM); j <= min((i - 1) * 1ll, totN); ++j) {
      if (flag[j - 1][i - j] || flag[j][i - j - 1]) {
        MIN = min(MIN, mapp[j][i - j]);
      }
      putchar(MIN);
      for (int j = max(1ll, i - totM); j <= min((i - 1) * 1ll, totN); ++j) {
        if (flag[j - 1][i - j] || flag[j][i - j - 1]) {
          if (mapp[j][i - j] == MIN) {
            flag[j][i - j] = true;
          }
        }
      }
    }
  }
  return 0;
} // Thomitics Code
```

## T2

### 题目

![](https://i0.hdslb.com/bfs/album/ea169b151f76fea71507c3e3872990899dcd3193.png)

### 题解

很显然，数据范围这么小，首先想到的是状压DP。（不过我还想过费用流，但是并没想到怎么做）在考场上我还以为状压DP这个复杂度，应该是那个40分的做法，没想到竟然过了。

### 代码

```cpp
/**************************************************************
 * Problem: OJ ProID
 * Author: 芊枫
 * Date: 2021 Mon Date
 * Algorithm:
 **************************************************************/
#include <algorithm>
#include <bits/stdc++.h>
#include <cstring>
#include <vector>
#define INF 0x7f7f7f7f7f7f7f7f
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

long long DP[21][1 << 21];
long long totN;
long long totK;
long long C[25][25];
long long cnt[23];
vector<long long> hva[23];
long long totANS = INF;

inline long long lowbit(long long x) { return x & (-x); }

int count1(long long x) {
  int ncnt = 0;
  while (x) {
    x -= lowbit(x);
    ++ncnt;
  }
  return ncnt;
}

void prefix() {
  for (long long i = 1; i <= 1 << 20; ++i) {
    int nw = count1(i);
    ++cnt[nw];
    hva[nw].push_back(i);
  }
  memset(DP[totN], 0, sizeof DP[totN]);
}

int main() {
  totN = read();
  totK = read();
  for (int i = 1; i <= totN; ++i) {
    for (int j = 1; j <= totN; ++j) {
      C[i][j] = read();
    }
  }
  memset(DP, 0x7f, sizeof(DP));
  prefix();
  for (int i = totN; i >= totK; --i) {
    for (int j = 1; j <= totN; ++j) {
      for (int k = 1; k <= totN; ++k) {
        for (auto s : hva[i]) {
          if (s & (1 << j)) {
            DP[i - 1][s ^ (1 << j) | (1 << k)] =
                min(DP[i - 1][s ^ (1 << j) | (1 << k)], DP[i][s] + C[j][k]);
          }
        }
      }
    }
  }
  for (auto i : hva[totK]) {
    totANS = min(totANS, DP[totK][i]);
  }
  write(totANS);
  return 0;
} // Thomitics Code

```

## T3

### 题目

![](https://i0.hdslb.com/bfs/album/767d45eb379bb8bf516a2a27e097168d710d45a4.png)

### 思路

我们可以注意到解是严格递增子序列和严格递减子序列的并集，同时使得严格递增子序列的最
大元素小于严格递减子序列的最小元素。

因为如果我们固定其中一个数一定要在里面，为了形成最长的严格递增子序列，在这个数之后
的数，我们肯定会把比这个数小的放在左边，比这个数大的放在右边。显然放到左边的递增的，本
来是递减的。

比如对于位置 $X$，我们只考虑 $X$ 右边的数造成的影响，如果 $A$ 是以 $X$ 位置（包括 $X$ 位置）
结束的最严格递增子序列的长度，$B$ 是严格递减子序列的长度，如果 $numA 、numB$ 分别是获得
它们的方法数，则 $X$（包括 $X$ 位置）右侧的数字的最大长度为 $A + B − 1$ ，得到这个解的方法有
很多 $numA × numB$。

我们用 $R$ 表示这个最大可能的递增子序列的长度。那么我们可以得到这个长度的路径数，就
是所有可以达到最大长度 $R$ 的位置数量乘以 $2N−R$（剩下的位置任意）。

时间复杂度为 $O(n log n)$ 。

## T4

### 题目

![](https://i0.hdslb.com/bfs/album/6aeecc298ce09a995900356ce215dac1d838af3e.png)

### 题解

![](https://i0.hdslb.com/bfs/album/0f48b93a5e6aede42d82ce723dadaa6902cdb36a.png)