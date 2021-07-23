---
title: 正睿OI Day7 总结
date: 2021-07-23
tags:
 - 培训总结
 - 考试
categories: 培训总结
description: 正睿OI Day7总结。
top_img: https://api.dujin.org/bing/1920.php
cover: https://tva3.sinaimg.cn/large/0072Vf1pgy1fodqo2pcz9j31kw0zkkjl.jpg
---

## T1

### 题目

Cuber QQ 现在在数轴上放了 $n$ 个点，每个点有一个坐标 $a_i$。现在 Cuber QQ 需要用数个宽度为 $k$ 的框体覆盖数轴上全部 $n$ 个点，Cuber QQ 想知道最少需要的框体数量可以覆盖所有的点。

### 思路

很简单，贪心，尽量往后放即可。

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
long long totN;
long long totK;
long long A[maxN];
long long F[maxN];
bool ied[maxN];
long long totANS;
long long LAST;

int main() {
  totN = read();
  totK = read();
  for (int i = 1; i <= totN; ++i) {
    A[i] = read();
  }
  sort(A + 1, A + totN + 1);
  for (int i = 1; i <= totN; ++i) {
    if (LAST && A[i] - LAST <= totK) {
      continue;
    }
    ++totANS;
    LAST = A[i];
  }
  write(totANS);
  return 0;
} // Thomitics Code
```
## T2

### 题目

一个二维世界的山谷处下雨了。给定山谷中每个位置的高度和总下雨量，要求计算出下雨后水面的最高水位。

![](https://i0.hdslb.com/bfs/album/6926638af06072141ce138467f86270cebb92c5f.png)

左图为地形图，每个数字表示相应位置的地面高度，灰色表示地面。

右图中浅蓝色表示水面平衡后的水，下雨8单位后的最高水位为4。

山谷的左右边界无限高，底部就算高度为0也不漏水。

### 思路

首先可以发现，因为给定的是整个区域总共的下雨量，所以排序后，对答案没影响。
这样一来，其实可以发现，下雨的水，会从左到右慢慢的蔓延。
我们可以根据高度的变化，把雨水蔓延的不同宽度分为不同阶段，则每一个阶段都是一个矩形。
首先我们确定出最终会蔓延至哪一个矩形以及在那个矩形中剩余的水是多少，就可以直接计算
出水面的高度了。使用前缀和处理即可。

### 考场代码

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
long long totN;
long long totK;
long long nums[maxN];
long long l[maxN];
long long r[maxN];
long long sum[maxN];
long long len[maxN];
long long H;
long long LOW = INF;
long long LOWi;
long long maxC;
long long cUP;
long long cLOW;
bool lowFlag;
bool mapp[maxN][maxN];
bool nowFLAG;
long long maxH;

int main() {
  totN = read();
  totK = read();
  nums[0] = INF;
  for (int i = 1; i <= totN; ++i) {
    nums[i] = read();
    if (nums[i] > nums[i - 1]) {
      lowFlag = 1;
    }
    if (nums[i] < LOW) {
      LOW = nums[i];
      LOWi = i;
    }
    if (nums[i] > H) {
      H = nums[i];
    }
    if (!l[nums[i]] && !lowFlag) {
      l[nums[i]] = i;
    }
    if (i > r[nums[i]] && lowFlag) {
      r[nums[i]] = i;
    }
    for (int j = 1; j <= nums[i]; ++j) {
      mapp[i][j] = true;
    }
  }
  for (int i = 1; i <= 1020; ++i) {
    if (i > H) {
      len[i] = totN;
      sum[i] = len[i] + sum[i - 1];
      continue;
    }
    for (int j = 1; j <= totN; ++j) {
      nowFLAG = true;
      if (nowFLAG == true && !mapp[j][i]) {
        ++len[i];
      }
    }
    sum[i] = len[i] + sum[i - 1];
  }
  // for (int i = 1; i <= 1001; ++i) {
  //   // if (l[i] || r[i]) {
  //   len[i] = r[i] - l[i] + 1;
  //   // }
  //   // if ((l[i] == r[i]) && (i > LOWi)) {
  //   //   len[i] = totN - l[i] + 1;
  //   // } else if ((l[i] == r[i]) && (i < LOWi)) {
  //   //   len[i] = r[i];
  //   // } else if (l[i] == r[i] && i == LOW) {
  //   //   len[i] = 1;
  //   // }

  //   if (!l[i]) {
  //     len[i] = r[i];
  //   }
  //   if (!r[i]) {
  //     len[i] = totN - l[i] + 1;
  //   }
  //   if (i < LOW) {
  //     len[i] = 0;
  //   }
  //   if (i > H) {
  //     len[i] = totN;
  //   }
  //   sum[i] = sum[i - 1] + len[i];
  // }
  while (totK - sum[maxH + 1] >= 0) {
    ++maxH;
  }
  totK -= sum[maxH];
  if (totK != 0) {
    cUP = totK;
    cLOW = len[maxH + 1];
    long long G = __gcd(cUP, cLOW);
    cUP /= G;
    cLOW /= G;
  }
  if (totK != 0) {
    write(maxH);
    putchar('+');
    write(cUP);
    putchar('/');
    write(cLOW);
    putchar('\n');
  } else {
    write(maxH);
    putchar('\n');
  }
  return 0;
} // Thomitics Code
```

## T3

### 题目

我们已知中位数的定义：对于一个有序数组 $A \{ a_1,a_2,\cdots ,a_n \}$ ，如果 $n$ 是奇数，那么它的中位数为 $a_{\frac{n+1}{2}}$ ；如果 $n$ 为偶数，那么它的中位数为 $\frac {a_{n/2}+a_{(n/2+1)}}{2}$ 。

现有两个有序数组 $A \{ a_1,a_2,\cdots ,a_m \}$ 和 $B \{ b_1,b_2,\cdots ,b_n \}$ ，需要查找数组 $A$ 和数组 $B$ 中所有元素的中位数。

由于一些原因，我们不得不加上一些修改的操作。

这道题目一共会给出 $q$ 个询问，每个询问都是都是将数组 $A$ 或者数组 $B$ 的元素全部加上一个整数 $x$ ，显然一个有序的数组全部加上一个整数 $x$ 以后仍然有序，你需要回答的是，经过操作以后的数组 $A$ 和数组 $B$ 中所有元素的中位数。

查询操作造成的修改不是连续性的，即每次的修改操作都是独立的。

### 思路

#### 80 pts
直接二分查找。

其实就是需要找到两个有序数组合并得到一个大的有序数组后中间位置的元素，我们可以在第
一个数组中二分，再次二分判断在第二个数组中比他小的数有多少个，来计算这个数的排名来确定
他是不是中位数；同样我们也可以在第二个数组中二分，再次二分判断在第一个数组中比他小的数
有多少个，来计算这个数的排名来确定他是不是中位数。
#### 100 pts
上一种方法中时间复杂度有两个 $log$ ，能不能考虑优化掉一个 $log$ 呢。

我们考虑一下中位数的含义：将一个集合划分为两个长度相等的子集，其中一个子集中的元素总是大于另一个子集中的元素。

首先，在任意位置 $i$ 将 $A$ 划分成两个部分：$A[0], A[1], ..., A[i − 1]$ 和 $A[i], A[i + 1], ..., A[m − 1]$（我们把左边记为 $left_A$，右边记为 $right_A$）。由于 $A$ 中有 $m$ 个元素，所以有 $m + 1$ 种划分的方法$(i ∈ [0, m])$。

采用同样的方式，在任意位置 $j$ 将 $B$ 划分成两个部分：$left_B$ 和 $right_B$ 。

将 $left_A$ 和 $left_B$ 放入一个集合，并将 $right_A$ 和 $right_B$ 放入另一个集合。再把这两个
新的集合分别命名为 $leftpart$ 和 $rightpart$。

当 A 和 B 的总长度是偶数时，如果可以确认：

• $len(leftpart) = len(rightpart)$

• $max(leftpart) ≤ min(rightpart)max(part) min(part)$

那么，${A, B}$ 中的所有元素已经被划分为相同长度的两个部分，且前一部分中的元素总是小于或等于后一部分中的元素。中位数就是前一部分的最大值和后一部分的最小值的平均值。

当 $A$ 和 $B$ 的总长度是奇数时，如果可以确认：

• $len(lef tpart) = len(rightpart) + 1$

• $max(leftpart) ≤ min(rightpart)max(part) min(part)$

那么，${A, B}$中的所有元素已经被划分为两个部分，前一部分比后一部分多一个元素，且前一部分中的元素总是小于或等于后一部分中的元素。中位数就是前一部分的最大值：

$median = max(leftpart)median = max(leftpart)$

也就是说，如果我们在 $A$ 中二分 $i$ 的位置，可以直接确定出 $j$ 的位置，根据第一个条件，然
后调整。
### 考场豹力代码

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
long long cntA[maxN];

// struct Node {
//   long long val;
//   long long tag;
//   long long l, r;
//   Node *lch, *rch;
//   void pushup() { val = lch->val + rch->val; }
//   Node(long long L, long long R,bool tr) {
//     l = L;
//     r = R;
//     tag = 0;
//     val = 0;
//     if (lch == rch) {
//       if (tr) {
//         val = cntB[l];
//       }

//     }
//   }
// };

// int merge(int a, int b, int x, int y) {
//   if (!a)
//     return b;
//   if (!b)
//     return a;
//   if (x == y) {
//     d[a] += d[b];
//     t[a] = x;
//     return a;
//   }
//   int mid = x + y >> 1;
//   l[a] = merge(l[a], l[b], x, mid), r[a] = merge(r[a], r[b], mid + 1, y);
//   pushup(a);
//   return a;
// }

long long totN;
long long totM;
long long totQ;
long long numA[maxN];
long long numB[maxN];
long long tmpA[maxN];
long long tmpB[maxN];
long long fNUM[maxN];

int main() {
  totN = read();
  totM = read();
  totQ = read();
  for (int i = 1; i <= totN; ++i) {
    numA[i] = read();
  }
  for (int i = 1; i <= totM; ++i) {
    numB[i] = read();
  }
  for (int i = 1; i <= totQ; ++i) {
    short tmp = read();
    long long x = 0;
    for (int j = 1; j <= totN; ++j) {
      tmpA[j] = numA[j];
    }
    for (int j = 1; j <= totM; ++j) {
      tmpB[j] = numB[j];
    }
    if (tmp == 1) {
      x = read();
      for (int j = 1; j <= totN; ++j) {
        tmpA[j] += x;
      }
    }
    if (tmp == 2) {
      x = read();
      for (int j = 1; j <= totM; ++j) {
        tmpB[j] += x;
      }
    }
    long long Ai = 0;
    long long Bi = 0;
    if (tmpA[1] <= tmpB[1]) {
      fNUM[1] = tmpA[1];
      ++Ai;
    } else {
      fNUM[1] = tmpB[1];
      ++Bi;
    }
    while (Ai + Bi <= ((totN + totM) >> 1) + 1) {
      if (Ai + 1 > totN) {
        ++Bi;
        fNUM[Ai + Bi] = tmpB[Bi];
        continue;
      }
      if (Bi + 1 > totM) {
        ++Ai;
        fNUM[Ai + Bi] = tmpA[Ai];
        continue;
      }
      if (tmpA[Ai + 1] <= tmpB[Bi + 1]) {
        ++Ai;
        fNUM[Ai + Bi] = tmpA[Ai];
      } else {
        ++Bi;
        fNUM[Ai + Bi] = tmpB[Bi];
      }
    }
    if ((totN + totM) & 1) {
      write((fNUM[((totN + totM) >> 1) + 1]));
      putchar('\n');
    } else {
      if ((fNUM[(totN + totM) >> 1] + fNUM[((totN + totM) >> 1) + 1]) & 1) {
        write((fNUM[(totN + totM) >> 1] + fNUM[((totN + totM) >> 1) + 1]));
        puts(".5");
      } else {
        write((fNUM[(totN + totM) >> 1] + fNUM[((totN + totM) >> 1) + 1]));
      }
    }
  }
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

#include <algorithm>
#include <cstdio>
#include <cstring>
#include <iostream>
#include <map>
#include <queue>
#include <set>
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
long long totN;
long long totM;
long long totQ;
long long numA[maxN];
long long numB[maxN];
long long Δa, Δb;
long long totANS;

long long check(long long P, long long Q) {
  if (Q < 0) {
    return 1;
  }
  if (P < 0) {
    return -1;
  }
  if (Q > totM) {
    return -1;
  }
  if (P > totN) {
    return 1;
  }
  if (max(numA[P] + Δa, numB[Q] + Δb) <=
      min(numA[P + 1] + Δa, numB[Q + 1] + Δb)) {
    return 0;
  }
  if (numA[P] + Δa > numB[Q + 1] + Δb) {
    return -1;
  } else {
    return -1;
  }
}

long long bin(long long Kth) {
  long long nowP;
  long long nowANS;
  long long L = -1;
  long long R = totN + totM + 1;
  long long Mid;
  long long nowT;
  while (true) {
    Mid = (L + R) >> 1;
    nowT = check(Mid, Kth - Mid);
    if (!nowT) {
      nowP = Mid;
      break;
    }
    if (nowT < 0) {
      L = Mid + 1;
    } else {
      R = Mid;
    }
  }
  nowANS = min(numA[nowP + 1] + Δa, numB[Kth - nowP + 1] + Δb);
  return nowANS;
}

int main() {
  totN = read();
  totM = read();
  totQ = read();
  for (int i = 1; i <= totN; ++i) {
    numA[i] = read();
  }
  for (int i = 1; i <= totN; ++i) {
    numB[i] = read();
  }
  numA[0] = numB[0] = -INF;
  numA[totN + 1] = numB[totM + 1] = INF;
  for (int i = 1; i <= totQ; ++i) {
    short tmp = read();
    Δa = Δb = 0;
    if (tmp == 1) {
      Δa = read();
    }
    if (tmp == 2) {
      Δb = read();
    }
    totANS = bin((totN + totM - 1) / 2) + bin((totN + totM) / 2);
    write(totANS / 2);
    if (totANS & 1) {
      puts(".5");
    }
  }
  return 0;
} // Thomitics Code

```

## T4

### 题目

Cuber QQ 是个喜欢数学的学生。有一天，他开始研究起一些奇怪的数，他把这些数叫做~~神里~~神数。

神数的定义是：这个数十进制字符串（不包括前导零）含有所有给定的 $n$ 个字符串。

但是这样的数实在是太多了，Cuber QQ 需要你的帮助：给定 $l$ 和 $r$，你需要求 

$$\sum_{i = l} ^ r i \cdot \left[ i\, \text{是神数}\right] \pmod {998244353}$$

其中 

$$\left[P\right] = \begin{cases}
1,&amp; P \text{为真}\\
0,&amp; P \text{为假}
\end{cases}$$ 

也就是求区间中所有是神数的数的和。

### 思路

这个题比较难想，也很难实现。

要求包含所有给定字符串的数之和。

考虑在 $\text{AC}$ 自动机上 $\text{DP}$ 。

用 $f[i][j][state]$ 表示处理到数的第 $i$ 位，对应 $AC$ 自动机上状态 $j$，包含给定字符串的状态为$state$ 。（$state$ 中如果完整包含了一个字符串，则用 $1$ 表示，否则用 $0$ 表示）。

接下来就是比较经典的数位 $DP$ 的套路，即需要在 $DP$ 之前状态的基础上，继续加上一些状态$[lead = 0/1][up = 0/1]$ 表示当前状态是否有前导 $0$ ，是否有上限的限制。

有了状态表示，转移就比较方便了，枚举每一位可能取的数字考虑其在 $AC$ 自动机上的状态转移，计数。

### 代码

```cpp
#include <bits/stdc++.h>

using namespace std;

#define INF 0x3f3f3f3f3f3f3f3f
#define IINF 0x3f3f3f3f

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

const int maxn = 1 << 8;
const int mod = 119 << 23 | 1;
int p[maxn];

struct Trie {
  int ch[maxn][10], f[maxn], val[maxn];
  int sz, rt;
  int newnode() {
    memset(ch[sz], -1, sizeof(ch[sz])), val[sz] = 0;
    return sz++;
  }
  inline int idx(char c) { return c - '0'; };
  void insert(const char *s, int id) {
    int u = 0;
    for (int i = 0; s[i]; i++) {
      int c = idx(s[i]);
      if (!~ch[u][c])
        ch[u][c] = newnode();
      u = ch[u][c];
    }
    val[u] |= (1 << id);
  }
  void build() {
    queue<int> q;
    f[rt] = rt;
    for (int c = 0; c < 10; c++) {
      if (~ch[rt][c])
        f[ch[rt][c]] = rt, q.push(ch[rt][c]);
      else
        ch[rt][c] = rt;
    }
    while (!q.empty()) {
      int u = q.front();
      q.pop();
      val[u] |= val[f[u]];
      for (int c = 0; c < 10; c++) {
        if (~ch[u][c])
          f[ch[u][c]] = ch[f[u]][c], q.push(ch[u][c]);
        else
          ch[u][c] = ch[f[u]][c];
      }
    }
  }
  vector<int> l, r;
  int all;
  pair<int, int> dp[maxn][maxn][1 << 5];
  bool vis[maxn][maxn][1 << 5];
  void init(int n) {
    all = (1 << n) - 1;
    sz = 0, rt = newnode();
    set(l), set(r);
    assert(l.size() <= r.size());
    l.resize(r.size());
  }
  void set(vector<int> &x) {
    static char buf[maxn];
    scanf("%s", buf);
    int n = strlen(buf);
    x.resize(n);
    for (int i = 0, j = n - 1; ~j; i++, j--)
      x[i] = buf[j] - '0';
  }
  pair<int, int> dfs(int pos, int u, int state = 0, bool lead = true,
                     bool limit_l = true, bool limit_r = true) {
    if (!~pos)
      return make_pair(state == all, 0);
    if (!limit_l && !limit_r && !lead && vis[pos][u][state])
      return dp[pos][u][state];
    pair<int, int> ans = {0, 0};
    for (int c = 0; c < 10; c++) {
      if (limit_l && c < l[pos])
        continue;
      if (limit_r && c > r[pos])
        continue;
      int v = u;
      if (!lead || c)
        v = ch[u][c];
      auto tmp = dfs(pos - 1, v, state | val[v], lead && c == 0,
                     limit_l && c == l[pos], limit_r && c == r[pos]);
      ans.first += tmp.first;
      if (ans.first >= mod)
        ans.first -= mod;
      int o = 1LL * c * p[pos] % mod;
      ans.second += (1LL * o * tmp.first % mod + tmp.second) % mod;
      if (ans.second >= mod)
        ans.second -= mod;
    }
    if (!limit_l && !limit_r && !lead) {
      vis[pos][u][state] = true;
      dp[pos][u][state] = ans;
    }
    return ans;
  }
  int solve() {
    auto ans = dfs(r.size() - 1, rt);
    return ans.second;
  }
} ac;

int main() {
  p[0] = 1;
  for (int i = 1; i < maxn; i++)
    p[i] = 10LL * p[i - 1] % mod;
  int n;
  scanf("%d", &n);
  ac.init(n);
  for (int i = 0; i < n; i++) {
    static char buf[1 << 10];
    scanf("%s", buf);
    ac.insert(buf, i);
  }
  ac.build();
  printf("%d\n", ac.solve());
}
```

## Ex1

### 题目

给出一个$N$层的方格金字塔,自顶向下依次标号为第$1$到第$N$层。  

![](https://cdn.luogu.com.cn/upload/vjudge_pic/AT2163/a992c42b0e9b0597f104bf82a0adc1131324bb4f.png)

其中第i($1 \le i \le N$)层有$2i − 1$个方格。（具体形态见下面的图）  

第$N$层有一个$1$到$2N-1$的排列,其他层的数字按以下规则生成：方格$b$中填写的整数,是方格$b$正下方,左下方和右下方方格中所写整数的中位数。

![](https://cdn.luogu.com.cn/upload/vjudge_pic/AT2163/545e109d7af3caf92b1a8f9ac80715efa6c3d3db.png)

现在请你构造出一组第$N$层的数字，使得求得的第一层的数字为$X$。

### 思路

这道例题看到时毫无头绪，往二分的方向想，猜到是二分枚举最上面的那个数是多少，但依然不会做。

事实上，因为我们求得是中位数，所以每个数对我们来说只有与枚举的数的大小关系需要我们考虑，我们可以使大于这个数的数为1，其余的为0。

如此将其转化成一个01串，接下来我们讨论这个01串。

接下来我们可以小小的推一下。事实上，当有两个都为0的数或者都为1的数，那么就可以一直往上走。

简单地表示一下。

我们有这样一个 0 1 数列
```
                          0  1  1  0  1
```
然后我们继续推，会变成这样
```
							                  1 
                            
                             1  1  1  
                          
                          0  1  1  0  1
```
可以看到如果有两个一样的数挨在一起，他们就会一直往上走。

似乎我们只要找到相邻一样的就可以了。

例图:

![](https://cdn.luogu.com.cn/upload/pic/28097.png)

大概就是这个意思吧。

我们只需要找到离中间最近的一组相邻一样的两个数，就可以得到最上面是什么数。

那么就有人问了，如果有两个不同的相邻的离中间一样远呢？

......

再想一想。

因为一共只有两种数，又是奇数个，所以这种可能不存在。

最后还有一种特殊情况。就是没有任何两个相邻的。

大概长这样。。

![](https://cdn.luogu.com.cn/upload/pic/28098.png)

我们只需要特判一下就行了。

大概就是这样。

## Ex2

### 题目

> “你的地图是一张白纸，所以即使想决定目的地，也不知道路在哪里。”

QQ 小方最近在自学图论。他突然想出了一个有趣的问题：

一张由 $n$ 个点，$m$ 条边构成的有向无环图。每个点有点权 $A_i$。QQ 小方想知道所有起点为 $1$ ，终点为 $n$ 的路径中最大的中位数是多少。

一条路径的中位数指的是：一条路径有 $n$ 个点，将这 $n$ 个点的权值从小到大排序后，排在位置 $\left \lfloor \frac{n}{2} \right \rfloor +1$ 上的权值。

### 思路

跟刚才那道题十分相似。也是二分答案，对于大于等于Mid的设为1，小于Mid设为0，然后其实找到1比零多最多的路径即可。我们仔细想想，这种思路好像很难实现。很难找出哪条路径1比0多的最多。

所以，我们考虑转化一下。

怎么转化？刚才说了，要找的是1比0多的最多的路径。所以，只需要将大于等于Mid的设为1，小于Mid设为-1就可以了！