---
title: 正睿OI Day6 总结
date: 2021-07-22
tags:
 - 培训总结
 - 字符串
categories: 培训总结
description: 正睿OI Day6总结。
top_img: https://api.dujin.org/bing/1920.php
cover: https://tvax4.sinaimg.cn/large/0072Vf1pgy1foxlhcoxbbj31kw0w0tz3.jpg
---

## T1

### 题目

给出一串字符串s

求出所有s的前缀在s中出现次数之和

$字符串长度 <= 200000$

例如 abab 答案 6
```
a 两次匹配
ab 两次匹配
aba 一次匹配
abab 一次匹配
```

### 思路

考虑利用next数组。因为next代表前缀在后面的后缀里面出现了一次。在前缀的后缀中出现的前缀的前缀，也会在这个后缀中出现一次。所以通过next跳着往前找。每嵌套一个next，贡献+1.

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
inline void write(const long long &x) {
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

const int MAXN = 1000090;
char A[MAXN], B[MAXN];
long long KMP[MAXN];
long long LA, LB;
long long totANS;

void prefix() {
  int j = 0;
  for (int i = 2; i <= LB; ++i) {
    while (j && (B[i] != B[j + 1])) {
      j = KMP[j];
    }
    if (B[i] == B[j + 1]) {
      ++j;
    }
    KMP[i] = j;
  }
}

int main() {
  scanf("%s", B + 1);
  LB = strlen(B + 1);
  prefix();
  long long j;
  for (int i = 1; i <= LB; i++) {
    long long ti = i;
    while (ti > 1) {
      ti = KMP[ti];
      ++totANS;
    }
  }
  write(totANS);
  return 0;
} // Thomitics Code
```

## T2

### 题目1

求字符串的最短的循环节

即S 的最短循环节是 A 当且仅当 S=AAA..AAA

### 题目2

求字符串的最短的循环节

即S 的最短循环节是 A 当且仅当 S 是 AAA..AAA 的 substring


### 思路

![](https://i0.hdslb.com/bfs/album/e3f1ee01b6a23e37a227c7f1c61689c7663d50d5.jpg)

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
    if ((LEN / (LEN - nxt[LEN]))*(LEN - nxt[LEN])==LEN){
        write(LEN - nxt[LEN]);
    }else{
        putchar('1');
    }
    putchar('\n');
  }
  return 0;
} // Thomitics Code
```

## T3

### 题目

求字符串每一个前缀的最长循环节长度之和。

不算本身

### 思路

这是一道让我们深入了解KMP算法精髓的好题。

题目中的“匹配前缀”我们可以这样理解：在A的前缀中，把这个前缀再叠加一遍后就把A包括进来，如图：

![](https://i0.hdslb.com/bfs/album/0bd753c58f0b461d7367681763731b38143d48f5.png)

那么，"abcabcab"的最长匹配子串应该是"abcabc"，长度为6。

我们设第一个图中字符串为$S$，第二个字符串为$SS$，显然有$S[6..8]=SS[6..8]=SS[1..2]=S[1..2]$。于是我们得到规律，匹配前缀子串满足KMP算法中“前缀等于后缀”的性质，我们要使子串最长，那么这个匹配长度应该尽可能小。比如对于$S$来说,$next[8]$应该为5，表示$S[1..5]$和$S[4..8]$是匹配的，但我们选择的是最短的匹配长度$short[8]=2,S[1..2]=S[7..8]$，而答案就是$8-short[8]=6$。

但是KMP只能求出每个前缀串的最长匹配长度，如果要求出最短匹配长度，我们可以一直递推$next[i],next[next[i]]...$，直到为$0$. 熟悉的KMP本质的人都应该知道为什么，这里举一个例子。

在$S$中，$next[8]=5$,而$next[5]=2，next[2]=0$了，所以$next[5]=2$就是$8$的最短匹配长度，将$8-2$累计到答案中即可。

最后，类似求$next$时的递推方法，我们可以递推$short$来提高效率。比如在上例中,我们得到$short[8]=2$后，就直接将$next[8]$修改为$2$，这样$8$以后的数字如果递推到8了就可以直接跳到$next[2]=0$，而不用跳到$next[5]$这里。

### 代码

```cpp
/**************************************************************
 * Problem: Luogu P3435
 * Author: 芊枫
 * Date: 2021 July 22
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
inline void write(const long long &x) {
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

const int MAXN = 90;
char A[MAXN], B[MAXN];
long long KMP[MAXN];
long long LA, LB;
long long totANS;

void prefix() {
  int j = 0;
  for (int i = 2; i <= LB; ++i) {
    while (j && (B[i] != B[j + 1])) {
      j = KMP[j];
    }
    if (B[i] == B[j + 1]) {
      ++j;
    }
    KMP[i] = j;
  }
}

int main() {
  LB = read();
  scanf("%s", B + 1);
  prefix();
  long long j;
  for (int i = 1; i <= LB; ++i) {
    j = i;
    while (KMP[j]) {
      j = KMP[j];
    }
    if (KMP[i]) {
      KMP[i] = j;
    }
    totANS += i - j;
  }
  write(totANS);
  return 0;
} // Thomitics Code
```

## T4

### 题目

The little cat is so famous, that many couples tramp over hill and dale to Byteland, and asked the little cat to give names to their newly-born babies. They seek the name, and at the same time seek the fame. In order to escape from such boring job, the innovative little cat works out an easy but fantastic algorithm:

Step1. Connect the father's name and the mother's name, to a new string S.
Step2. Find a proper prefix-suffix string of S (which is not only the prefix, but also the suffix of S).

Example: Father='ala', Mother='la', we have S = 'ala'+'la' = 'alala'. Potential prefix-suffix strings of S are {'a', 'ala', 'alala'}. Given the string S, could you help the little cat to write a program to calculate the length of possible prefix-suffix strings of S? (He might thank you by giving your baby a name:)

### 思路

其实就是给一个字符串，问你前缀和后缀相同的位置有哪些。

我们只需要看`next[n]`嵌套有几个就可以了。

### 代码
```cpp
/**************************************************************
 * Problem: Luogu P3435
 * Author: 芊枫
 * Date: 2021 July 22
 * Algorithm: KMP
 **************************************************************/

#include <algorithm>
#include <cstdio>
#include <cstring>
#include <iostream>
#include <map>
#include <queue>
#include <set>
#include <string>
#include <vector>

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
inline void write(const long long &x) {
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

const int MAXN = 1000090;
char A[MAXN], B[MAXN];
long long KMP[MAXN];
long long LA, LB;
long long totANS;
vector<int> nums;

void prefix() {
  int j = 0;
  for (int i = 2; i <= LB; ++i) {
    while (j && (B[i] != B[j + 1])) {
      j = KMP[j];
    }
    if (B[i] == B[j + 1]) {
      ++j;
    }
    KMP[i] = j;
  }
}

int main() {
  while (~scanf("%s", B + 1)) {
    LB = strlen(B + 1);
    prefix();
    long long j = LB;
    while (j) {
      nums.push_back(j);
      j = KMP[j];
    }
    sort(nums.begin(), nums.end());
    for (auto i : nums) {
      write(i);
      putchar(' ');
    }
    putchar('\n');
    nums.clear();
  }
  return 0;
} // Thomitics Code
```

## 拓展KMP

### 问题

给定两个字符串S和T（长度分别为n和m），下标从0开始，定义extend[i]等于S[i]...S[n-1]与T的最长相同前缀的长度，求出所有的extend[i]

### 定义

![](https://i0.hdslb.com/bfs/album/c2f539fd4f352b43a4898115c8a2857bc955304e.png)

$p$代表以$a$为起始位置的字符匹配成功的最右边界

$S[a...p)$等于$T[0...p-a)$

### 辅助数组 Next

其中next[i]含义为：T[i]...T[m - 1]与T的最长相同前缀长度，m为串T的长度。举个例子：

![](https://i0.hdslb.com/bfs/album/cc61670b8bd682eb757ea0665a9a0a2ae0c68b6b.png)

与KMP类似，求解next[i]的过程就是T自己和自己的一个匹配过程

### 计算

#### 情况1
$S[i]$对应$T[i - a]$，如果$i + next[i - a] < p$

如图，三个椭圆长度相同，根据next数组的定义，此时$extend[i] = next[i - a]$

![](https://i0.hdslb.com/bfs/album/f28015661cf5032d7aaaf00c5d34b49dbb5bb857.png)

#### 情况2

如果$i + next[i - a] == p$

如图，三个椭圆都是完全相同的

$S[p] != T[p - a]且T[p - i] != T[p - a]$，但$S[p]$有可能等于$T[p - i]$

所以我们可以直接从$S[p]$与$T[p - i]$开始往后匹配

![](https://i0.hdslb.com/bfs/album/73d0819d848daa8c82a47b912534d184a2fd1093.png)

#### 情况3

如果$i + next[i - a] > p$

那说明$S[i...p)$与$T[i-a...p-a)$相同

注意到$S[p] != T[p - a]$且$T[p - i] == T[p - a]$，也就是说$S[p] != T[p - i]$，所以就没有继续往下判断的必要了
所以 $extend[i]=p - i$。

![](https://i0.hdslb.com/bfs/album/61a4f03005090f5adce62cd212e8c720e0ae2cbf.png)


### 代码

```cpp
const long long maxN = 100090;
long long nxt[maxN];
long long ext[maxN];
string Ss, Tt;

void getNEXT(string T) {
  int Tlen = T.length();
  nxt[0] = Tlen;
  int now = 0;
  while (T[now] == T[now + 1] && now + 1 < Tlen) {
    ++now;
  }
  nxt[1] = now;
  int px = 1;
  for (int i = 2; i < Tlen; ++i) {
    if (i + nxt[i - px] < nxt[px] + px) {
      nxt[i] = nxt[i - px];
    } else {
      int now = nxt[px] + px - i;
      now = max(now, 0);
      while (T[now] == T[i + now] && i + now < Tlen) {
        ++now;
      }
      nxt[i] = now;
      px = i;
    }
  }
}

void ExKMP(string S, string T) {
  getNEXT(T);
  int Slen = S.length();
  int Tlen = T.length();
  int Mlen = min(Slen, Tlen);
  int now = 0;
  while (S[now] == T[now + 1] && now + 1 < Mlen) {
    ++now;
  }
  nxt[0] = now;
  int px = 0;
  for (int i = 2; i < Slen; ++i) {
    if (i + nxt[i - px] < nxt[px] + px) {
      ext[i] = nxt[i - px];
    } else {
      int now = nxt[px] + px - i;
      now = max(now, 0);
      while (T[now] == S[i + now] && i + now < Slen && now < Tlen) {
        ++now;
      }
      ext[i] = now;
      px = i;
    }
  }
}
```

## T5

### 题目

给定两个字符串

在第一个字符串中找到一个最大前缀，同时也是第二个字符串的后缀

### 思路

基本是拓展KMP板子题。但是并不完全是，因为ExKMP求出的是后缀的前缀与前缀的匹配，但是我们要的是后缀和前缀的匹配，所以对于每个匹配看看是不是完整的后缀就可以了。

### 代码

```cpp
/**************************************************************
 * Problem: HDU 2594
 * Author: 芊枫
 * Date: 2021 July 22
 * Algorithm: 拓展KMP
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

const long long maxN = 100000090;
long long nxt[maxN];
long long ext[maxN];
char Ss[maxN], Tt[maxN];

void getNEXT(char *T) {
  int Tlen = strlen(T);
  nxt[0] = Tlen;
  int now = 0;
  while (T[now] == T[now + 1] && now + 1 < Tlen) {
    ++now;
  }
  nxt[1] = now;
  int px = 1;
  for (int i = 2; i < Tlen; ++i) {
    if (i + nxt[i - px] < nxt[px] + px) {
      nxt[i] = nxt[i - px];
    } else {
      int now = nxt[px] + px - i;
      now = max(now, 0);
      while (T[now] == T[i + now] && i + now < Tlen) {
        ++now;
      }
      nxt[i] = now;
      px = i;
    }
  }
}

void ExKMP(char *S, char *T) {
  getNEXT(T);
  int Slen = strlen(S);
  int Tlen = strlen(T);
  int Mlen = min(Slen, Tlen);
  int now = 0;
  while (S[now] == T[now] && now < Mlen) {
    ++now;
  }
  ext[0] = now;
  int px = 0;
  for (int i = 1; i < Slen; ++i) {
    if (i + nxt[i - px] < ext[px] + px) {
      ext[i] = nxt[i - px];
    } else {
      int now = ext[px] + px - i;
      now = max(now, 0);
      while (T[now] == S[i + now] && i + now < Slen && now < Tlen) {
        ++now;
      }
      ext[i] = now;
      px = i;
    }
  }
}

int main() {
  while (~scanf("%s%s", Tt, Ss)) {
    ExKMP(Ss, Tt);
    long long maxx = 0;
    for (int i = 0; i < strlen(Ss); ++i) {
      if (ext[i] > maxx && ext[i] + i == strlen(Ss)) {
        maxx = ext[i];
      }
    }
    if (maxx) {
      for (int i = 0; i < maxx; ++i) {
        putchar(Tt[i]);
      }
      putchar(' ');
      write(maxx);
    } else {
      putchar('0');
    }
    putchar('\n');
  }
  return 0;
} // Thomitics Code
```

## T6 

### 题目

给一个字符串 $s[0…len−1] ,len \leq 10^6$

计算 the length of the longest common prefix of $s[i…len−1]$ and $s[0…len−1]$ for each $i>0$.  

求 compare operation次数。

![](https://i0.hdslb.com/bfs/album/5aca66f881ba08be6001b011662d9fc67d2b60ba.png)

### 思路

考虑这个是匹配到能匹配的最后一位就停止，不会浪费其它的匹配。所以我们知道只需要计算出来每次可以匹配多少就行了。再看一眼题，这要求的，不就是我们最熟悉的ExKMP的next数组吗？所以我们只需要把next数组里面所有数加起来就行了。

### 代码

```cpp
/**************************************************************
 * Problem: HDU 2594
 * Author: 芊枫
 * Date: 2021 July 22
 * Algorithm: 拓展KMP
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

const long long maxN = 100000090;
long long nxt[maxN];
long long ext[maxN];
char Ss[maxN], Tt[maxN];
long long totANS;

void getNEXT(char *T) {
  int Tlen = strlen(T);
  nxt[0] = Tlen;
  int now = 0;
  while (T[now] == T[now + 1] && now + 1 < Tlen) {
    ++now;
  }
  nxt[1] = now;
  int px = 1;
  for (int i = 2; i < Tlen; ++i) {
    if (i + nxt[i - px] < nxt[px] + px) {
      nxt[i] = nxt[i - px];
    } else {
      int now = nxt[px] + px - i;
      now = max(now, 0);
      while (T[now] == T[i + now] && i + now < Tlen) {
        ++now;
      }
      nxt[i] = now;
      px = i;
    }
  }
}

int main() {
  scanf("%s", Tt);
  getNEXT(Tt);
  for (int i = 0; i < strlen(Tt); ++i) {
    totANS += nxt[i];
  }
  write(totANS);
  return 0;
} // Thomitics Code
```
## 马拉车

### 思路
回文串是奇数或者是偶数

首先我们解决下奇数和偶数的问题，在每个字符间插入`"#"`，并且为了使得扩展的过程中，到边界后自动结束，在两端分别插入 `"^"` 和 `"$"`
回文串一定都是奇数

![](https://i0.hdslb.com/bfs/album/a5be28c4dd136240d99a2790807867ffadd913a3.png)

$p[i]$ 表示以 $i$ 为中心的最长回文的半径，例如

![](https://i0.hdslb.com/bfs/album/7eea326e10ecdc5f20df482780aaa9f83bfd2971.png)

所以 $P[i]-1$ 就是 回文串的长度

### 求解P

我们还需要记录两个变量，它们是 $maxRight$ 和 $center$ ，它们分别的含义如下：

$maxRight$ 表示记录当前向右扩展的最远边界，即从开始到现在使用“中心扩散法”能得到的回文子串，它能延伸到的最右端的位置 

$center$ 是与 $maxRight$ 相关的一个变量，它是上述 $maxRight$ 的回文中心的索引值

#### 情况1

现在我们要计算 $P[i]$

当 $i >= maxRight$ 的时候，这就是一开始，以及刚刚把一个回文子串扫描完的情况，此时只能够根据“中心扩散法”一个一个扫描，逐渐扩大 $maxRight$；

当 $i < maxRight$ 的时候，根据新字符的回文子串的性质，循环变量关于 $center$ 对称的那个索引（记为 $mirror$）的 $p$ 值就很重要。

#### 情况2
##### 情况2-1

$p[mirror]$ 的数值比较小，不超过 $maxRight - i$。

$maxRight - i$ 的值，就是从 $i$ 关于 $center$ 的镜像点开始向左走（不包括它自己），到 $maxRight$ 关于 $center$ 的镜像点的步数

![](https://i0.hdslb.com/bfs/album/796e2d7a0b9e6a8fefc489558078ee8b27f6dd6e.png)

##### 情况2-2

$p[mirror]$ 的数值恰好等于 $maxRight – I$

![](https://i0.hdslb.com/bfs/album/4de72495bbdb9f5e0a3c5493c4605cf9c9d349c6.png)

##### 情况2-3

$p[mirror]$ 的数值大于 $maxRight – I$

![](https://i0.hdslb.com/bfs/album/b054b1a9d1d6162d5f3d21ebfa4740734018afff.png)

## T7

### 题目

从字符串$𝐴$中取出可空子串$[𝑙1,𝑟1]$，从字符串$𝐵$中取出可空子串$[𝑙2,𝑟2]$，要求$𝑟1=𝑙2$且是回文串，长度最长能是多少。

$字符串长度 \leq 10^5$


### 思路

我们可以考虑这个新回文串由两个小串组成：一个的前缀和另一个后缀相镜像，其中一个字符串多出一个回文串，这样刚好组成一个回文串。考虑将其中一个字符串倒过来，然后匹配另一个。（拓展KMP）中间一部分使用马拉车，找出回文串。

## T8

### 题目

给一个多边形

求多边形有多少对称轴

$多边形顶点 \leq 100000$

$｜坐标｜\leq -1000 000 000$

### 思路

其实对称的多边形可以看做是一个回文串。我们将每条边拆成角度、边长，组成一个边长-角度-边长-角度的回文串，然后马拉车找出回文串，就是答案。

## T9

### 题目

阿狸喜欢收藏各种稀奇古怪的东西，最近他淘到一台老式的打字机。打字机上只有 $28$ 个按键，分别印有 $26$ 个小写英文字母和 `B`、`P` 两个字母。经阿狸研究发现，这个打字机是这样工作的：

* 输入小写字母，打字机的一个凹槽中会加入这个字母(这个字母加在凹槽的最后)。
* 按一下印有 `B` 的按键，打字机凹槽中最后一个字母会消失。
* 按一下印有 `P` 的按键，打字机会在纸上打印出凹槽中现有的所有字母并换行，但凹槽中的字母不会消失。

例如，阿狸输入 `aPaPBbP`，纸上被打印的字符如下：

```
a
aa
ab
```

我们把纸上打印出来的字符串从 $1$ 开始顺序编号，一直到 $n$。打字机有一个非常有趣的功能，在打字机中暗藏一个带数字的小键盘，在小键盘上输入两个数 $(x,y)$（其中 $1\leq x,y\leq n$），打字机会显示第 $x$ 个打印的字符串在第 $y$ 个打印的字符串中出现了多少次。

阿狸发现了这个功能以后很兴奋，他想写个程序完成同样的功能，你能帮助他么？

### 思路

#### 最暴力的
最暴力的方法：把所有询问代表的字符串跑一遍kmp然后输出

稍微优化一下:把所有询问保存起来，把模板串相同的合并，求出next然后匹配

但是这两种方法本质没有区别，都是暴力

#### 不那么暴力的
我们对于所有的串建立一个$AC$自动机，把询问按照$yy$排序，然后在AC自动机上面跑，每次跳$fail$更新答案

这样可以拿到70分，但是时间上限还是会$O\left(n^2\right)$左右

#### 巧妙的优化
这道题里面，所有的模板串和文本串都在AC自动机里

那么，题目中实际是在要求什么呢？

就是有多少个x串是y串的一个前缀的后缀

那么，在AC自动机自己身上有没有满足这样的检索的结构呢？

有的，那就是fail指针

trie上的某一个前缀的fail指针，指向的是作为它的最长后缀的那个节点；同时，从某个前缀开始一路沿着fail指针跳，直到根节点，过程中所有的节点代表的前缀都是这个前缀的后缀

也就是说，我们把fail指针看成树边，将这个“fail树”（不要和kmp的next树搞混了）提取出来，那么我们就可以把题目的询问变成这样：

把代表y串的所有前缀的节点打上标记，那么代表x串的节点的子树中的标记个数，就是这个询问的答案

维护个数和可以用fail树上的dfs序以及树状数组共同完成

#### 正解
上述过程中有一个重复的地方：每次我们都需要把树状数组归零，然后重新把新的y串前缀节点插进去——即使我们使用把y排序的方法也会TLE

但是这个过程中有一个问题：有些点会进进出出好多遍，并不高效，我们需要找到一个办法，使得每个AC自动机上的点只进出树状数组一次

那么谁能满足这个要求呢？

还是dfs序，只不过是原trie树上的dfs序

我们把输入的询问按照y串在trie树上的dfs序排序，依次加入、删除

因为按照dfs序遍历可以使每个点进入一次离开一次，所以这个方法的总时间效率只有$O\left(nlogn\right)$

这样这道题就做完了