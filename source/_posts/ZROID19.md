## T1

### 题目

吉吉出门时发现他忘带铅笔了。他决定在路上采购铅笔。他居住的城市可以抽象成一个加权无向图，共有 $n$ 个节点，$m$ 条道路，每条道路链接 $u_i$ 和 $v_i$ 两个城市，长度为 $w_i$。道路都是双向的。吉吉的家在 $1$ 号节点，学校在 $n$ 号节点。这个城市有 $n+m$ 个铅笔店，前 $n$ 个中的第 $i$ 个在第 $i$ 个节点上，后 $m$ 个中的第 $j$ 个在第 $j$ 条边上。吉吉想对于每个铅笔店，算出从他家到铅笔店再到学校的最短路径。

注意：对于每个在边上的铅笔店，吉吉必须**完整的经过这条边**。

### 思路

这个题其实很简单啊……不知道为什么考试的时候傻了，把$Dijkstra$想成单对单最短路了，浪费了好多时间……

嗯，固定经过一个点，我们可以想到`Floyd`是这样转移的。但是明显复杂度不行。考虑将每条路径分开，拆成$1$到$k$的和$k$到$n$的。对于经过边的也一样，就是拆成拆成$1$到$u$的和$v$到$n$的然后再加上边$(u,v)$本身就可以了。

跑两遍单源最短路，一次从1开始，一次从n开始。然后最后合并即可。考试的时候一开始没看见是双向边，还以为是$\text{Dijkstra}$写错了，浪费了一些时间。

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
#include <cstring>
#include <queue>
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

const long long maxN = 200090;
long long totN;
long long totM;
struct Edge {
  long long nxt;
  long long to;
  long long ID;
  long long val;
  long long pre;
} edges[maxN * 4];
long long cnt_edges;
long long head[maxN * 4];
long long Emin[maxN];
long long Nmin[maxN];
long long disA[maxN];
long long disB[maxN];
void add_edge(long long x, long long y, long long w, long long i) {
  ++cnt_edges;
  edges[cnt_edges].nxt = head[x];
  head[x] = cnt_edges;
  edges[cnt_edges].to = y;
  edges[cnt_edges].val = w;
  edges[cnt_edges].ID = i;
  edges[cnt_edges].pre = x;
}

struct Fe {
  long long to;
  long long pre;
  long long val;
} se[maxN * 4];

// long long cnt_nedges;
// long long nhead[maxN * 2];
// void add_nedge(long long x, long long y, long long w, long long i) {
//   ++cnt_nedges;
//   nedges[cnt_nedges].nxt = nhead[x];
//   nhead[x] = cnt_nedges;
//   nedges[cnt_nedges].to = y;
//   nedges[cnt_nedges].val = w;
//   nedges[cnt_nedges].ID = i;
//   nedges[cnt_nedges].pre = x;
// }

bool vis[maxN];
struct Node {
  long long dist;
  long long dot;
};
bool operator<(Node a, Node b) { return a.dist > b.dist; }
bool Dvis[100090];
void DijkstraA() {
  memset(Dvis, 0, sizeof(Dvis));
  memset(disA, 0x3f, sizeof disA);
  priority_queue<Node> Q;
  Q.push({0, 1});
  disA[1] = 0;
  while (!Q.empty()) {
    int x = Q.top().dot;
    Q.pop();
    if (Dvis[x]) {
      continue;
    }
    Dvis[x] = 1;
    for (int i = head[x]; i; i = edges[i].nxt) {
      long long y = edges[i].to;
      if (disA[y] > disA[x] + edges[i].val) {
        disA[y] = disA[x] + edges[i].val;
        if (!Dvis[y]) {
          Q.push({disA[y], y});
        }
      }
    }
  }
}

void DijkstraB() {
  memset(Dvis, 0, sizeof(Dvis));
  memset(disB, 0x3f, sizeof disB);
  priority_queue<Node> Q;
  Q.push({0, totN});
  disB[totN] = 0;
  while (!Q.empty()) {
    int x = Q.top().dot;
    Q.pop();
    if (Dvis[x]) {
      continue;
    }
    Dvis[x] = 1;
    for (int i = head[x]; i; i = edges[i].nxt) {
      long long y = edges[i].to;
      if (disB[y] > disB[x] + edges[i].val) {
        disB[y] = disB[x] + edges[i].val;
        if (!Dvis[y]) {
          Q.push({disB[y], y});
        }
      }
    }
  }
}

int main() {
  totN = read();
  totM = read();
  for (int i = 1; i <= totM; ++i) {
    long long x, y, w;
    x = read();
    y = read();
    w = read();
    add_edge(x, y, w, i);
    add_edge(y, x, w, i);
    se[i].pre = x;
    se[i].to = y;
    se[i].val = w;
  }
  DijkstraA();
  DijkstraB();
  for (int i = 1; i <= totN; ++i) {
    write(disA[i] + disB[i]);
    putchar('\n');
  }
  for (int i = 1; i <= totM; ++i) {
    // write(min(disA[edges[i].pre] + disB[edges[i].to] + edges[i].val,
    //           disA[edges[i].to] + disB[edges[i].pre] + edges[i].val));
    write(min(disA[se[i].pre] + disB[se[i].to] + se[i].val,
              disA[se[i].to] + disB[se[i].pre] + se[i].val));
    putchar('\n');
  }
  return 0;
} // Thomitics Code
```

## T2

### 题目

#### 题目描述
吉吉被传送到了一个森林中。森林是一个 $n \times m$ 的网格，每个格子可能是空地（用 `.` 表示），也可能是障碍物（用 `#` 表示）。吉吉一直珍藏着森林的地图，他能经过空地，但是他不能经过障碍物，也不能跑出森林。他事先在森林中设置了很多关键点（用 `+` 表示）。一开始吉吉会被传送到任意一个位置，如果他走到他设置的关键点，他就安全了。

因为吉吉是一个很跳的人，所以他的移动方式也十分奇怪。假设吉吉的初始位置是第 $a$ 行第 $b$ 列，那么他可以钦定两个整数 $x$ 和 $y$，并将 $a\leftarrow a + x, b \leftarrow b + y$。其中 $x, y$ 需要满足 $xy=0$，并且对于所有的 $\min\{a, a + x\} \le i \le \max\{a, a + x\}, \min\{b, b + y\} \le j \le \max\{b, b + y\}$，都有第  $i$ 行第 $j$ 列为空地。也就是说，吉吉每次可以跳过一个线段，满足线段上的所有格子都是空地。

由于某 $\text {C}$ 姓男子正在追捕他，所以吉吉想尽快回到他设置的关键点。你的任务就是对每个吉吉可能被传送到的位置（即：$1 \le i \le n, 1 \le j \le m$），都算出他到最近一个关键点的最短距离。


#### 输入格式
第一行两个正整数 $n$ 和 $m$，意义见题目描述。

接下来 $n$ 行，每行一个长度为 $m$ 的字符串，描述森林的地图。


#### 输出格式

输出 $n$ 行，每行 $m$ 个整数或字符，表示吉吉如果在第 $i$ 行第 $j$ 列，他到最近一个关键点的最短距离。如果该点为障碍物，输出 `#`；如果从该点出发吉吉不可能跳到关键点，输出 `X`；如果该点为关键点，输出 $0$；否则输出吉吉到关键点的最短距离。

### 思路

本来考场上想的DP+多源BFS……真的写吐了……不知道是怎样的毅力让我写出了这份代码：

```cpp
#define INF 0x3f3f3f3f3f3f3f3f
#define IINF 0x3f3f3f3f

#include <bits/stdc++.h>
#include <cstdio>
#include <cstring>
#include <queue>
using namespace std;

const int OutputBufferSize = 10000000;
namespace input {
#define BUF_SIZE 100000
#define OUT_SIZE 100000
#define ll long long
bool IOerror = 0;
inline char nc() {
  static char buf[BUF_SIZE], *p1 = buf + BUF_SIZE, *pend = buf + BUF_SIZE;
  if (p1 == pend) {
    p1 = buf;
    pend = buf + fread(buf, 1, BUF_SIZE, stdin);
    if (pend == p1) {
      IOerror = 1;
      return -1;
    }
  }
  return *p1++;
}
inline bool blank(char ch) {
  return ch == ' ' || ch == '\n' || ch == '\r' || ch == '\t';
}
inline void read(char &ch) {
  ch = nc();
  while (blank(ch))
    ch = nc();
}
inline void read(int &x) {
  char ch = nc();
  x = 0;
  for (; blank(ch); ch = nc())
    ;
  if (IOerror)
    return;
  for (; ch >= '0' && ch <= '9'; ch = nc())
    x = x * 10 + ch - '0';
}
#undef ll
#undef OUT_SIZE
#undef BUF_SIZE
}; // namespace input

namespace output {
char buffer[OutputBufferSize];
char *s = buffer;
inline void flush() {
  assert(stdout != NULL);
  fwrite(buffer, 1, s - buffer, stdout);
  s = buffer;
  fflush(stdout);
}
inline void print(const char ch) {
  if (s - buffer > OutputBufferSize - 2)
    flush();
  *s++ = ch;
}
inline void print(char *str) {
  while (*str != 0)
    print(char(*str++));
}
inline void print(int x) {
  char buf[25] = {0}, *p = buf;
  if (x == 0)
    print('0');
  while (x)
    *(++p) = x % 10, x /= 10;
  while (p != buf)
    print(char(*(p--) + '0'));
}
} // namespace output
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

const int maxN = 2005;
const int inf = 0x3f3f3f3f;
char s[maxN][maxN];
int totN, totM;
long long ANS[maxN][maxN];
long long DP[maxN][maxN][5];
long long mvs[6][2] = {{0, 0}, {1, 0}, {0, 1}, {-1, 0}, {0, -1}};
bool inQ[maxN][maxN];
long long StX[maxN];
long long StY[maxN];
long long cntSt;

struct Node {
  long long x, y;
};

// 1 up
// 2 down
// 3 right
// 4 left

// i 第几行
// j 第几列

void FS(long long XXX, long long YYY) {
  // for (int i = 1; i <= totN; ++i) {
  //   for (int j = 1; j <= totN; ++j) {
  //     if (s[i][j] == '#') {
  //       DP[i][j][0] = -1;
  //       DP[i][j][1] = -1;
  //       DP[i][j][2] = -1;
  //       DP[i][j][3] = -1;
  //       DP[i][j][4] = -1;
  //       continue;
  //     }
  //     if (s[i][j] == '+') {
  //       DP[i][j][0] = 0;
  //       DP[i][j][1] = 0;
  //       DP[i][j][2] = 0;
  //       DP[i][j][3] = 0;
  //       DP[i][j][4] = 0;
  //       continue;
  //     }
  //     DP[i][j][1] = min(DP[i - 1][j][1], DP[i][j][1]);
  //     DP[i][j][1] = min(DP[i - 1][j][3] + 1, DP[i][j][1]);
  //     DP[i][j][1] = min(DP[i - 1][j][4] + 1, DP[i][j][1]);
  //     DP[i][j][2] = min(DP[i + 1][j][2], DP[i][j][2]);
  //     DP[i][j][2] = min(DP[i + 1][j][3] + 1, DP[i][j][2]);
  //     DP[i][j][2] = min(DP[i + 1][j][4] + 1, DP[i][j][2]);
  //     DP[i][j][3] = min(DP[i][j + 1][1] + 1, DP[i][j][3]);
  //     DP[i][j][3] = min(DP[i][j + 1][2] + 1, DP[i][j][3]);
  //     DP[i][j][3] = min(DP[i][j + 1][1] + 1, DP[i][j][3]);
  //   }
  // }
  // 1 up
  // 2 down
  // 3 right
  // 4 left

  // i 第几行
  // j 第几列
  queue<Node> Q;
  memset(inQ, 0, sizeof inQ);
  Q.push({XXX, YYY});
  inQ[XXX][YYY] = true;
  while (!Q.empty()) {
    long long x = Q.front().x;
    long long y = Q.front().y;
    Q.pop();
    inQ[x][y] = false;
    // for (int i = 1; i <= 4; ++i) {
    //   long long tx = x + mvs[i][0];
    //   long long ty = y + mvs[i][1];
    // }
    if (x + 1 <= totN && s[x + 1][y] == '.') {
      if (DP[x][y][1] < DP[x + 1][y][1]) {
        if (DP[x][y][1]) {
          if (!inQ[x + 1][y]) {
            Q.push({x + 1, y});
            inQ[x + 1][y] = true;
          }
          DP[x + 1][y][1] = DP[x][y][1];
        } else {
          DP[x + 1][y][1] = 1;
        }
      }
      if (DP[x][y][3] + 1 < DP[x + 1][y][1]) {
        if (!inQ[x + 1][y]) {
          Q.push({x + 1, y});
          inQ[x + 1][y] = true;
        }
        DP[x + 1][y][1] = DP[x][y][3] + 1;
      }
      if (DP[x][y][4] + 1 < DP[x + 1][y][1]) {
        if (!inQ[x + 1][y]) {
          Q.push({x + 1, y});
          inQ[x + 1][y] = true;
        }
        DP[x + 1][y][1] = DP[x][y][4] + 1;
      }
    }

    //
    if (x - 1 >= 1 && s[x - 1][y] == '.') {
      if (DP[x][y][2] < DP[x - 1][y][2]) {
        if (DP[x][y][2]) {
          if (!inQ[x - 1][y]) {
            Q.push({x - 1, y});
            inQ[x - 1][y] = true;
          }
          DP[x - 1][y][2] = DP[x][y][2];
        } else {
          DP[x - 1][y][2] = 1;
        }
      }
      if (DP[x][y][3] + 1 < DP[x - 1][y][2]) {
        if (!inQ[x - 1][y]) {
          Q.push({x - 1, y});
          inQ[x - 1][y] = true;
        }
        DP[x - 1][y][2] = DP[x][y][3] + 1;
      }
      if (DP[x][y][4] + 1 < DP[x - 1][y][2]) {
        if (!inQ[x - 1][y]) {
          Q.push({x - 1, y});
          inQ[x - 1][y] = true;
        }
        DP[x - 1][y][2] = DP[x][y][4] + 1;
      }
    }

    //
    if (y + 1 <= totN && s[x][y + 1] == '.') {
      if (DP[x][y][1] + 1 < DP[x][y + 1][4]) {
        if (!inQ[x][y + 1]) {
          Q.push({x, y + 1});
          inQ[x][y + 1] = true;
        }
        DP[x][y + 1][4] = DP[x][y][1] + 1;
      }
      if (DP[x][y][2] + 1 < DP[x][y + 1][4]) {
        if (!inQ[x][y + 1]) {
          Q.push({x, y + 1});
          inQ[x][y + 1] = true;
        }
        DP[x][y + 1][4] = DP[x][y][2] + 1;
      }
      if (DP[x][y][3] < DP[x][y + 1][3]) {
        if (DP[x][y][3]) {
          if (!inQ[x][y + 1]) {
            Q.push({x, y + 1});
            inQ[x][y + 1] = true;
          }
          DP[x][y + 1][3] = DP[x][y][3];
        } else {
          DP[x][y + 1][3] = 1;
        }
      }
    }

    //
    if (y - 1 >= 1 && s[x][y - 1] == '.') {
      if (DP[x][y][1] + 1 < DP[x][y - 1][3]) {
        if (!inQ[x][y - 1]) {
          Q.push({x, y - 1});
          inQ[x][y - 1] = true;
        }
        DP[x][y - 1][3] = DP[x][y][1] + 1;
      }
      if (DP[x][y][2] + 1 < DP[x][y - 1][3]) {
        if (!inQ[x][y - 1]) {
          Q.push({x, y - 1});
          inQ[x][y - 1] = true;
        }
        DP[x][y - 1][3] = DP[x][y][2] + 1;
      }
      if (DP[x][y][4] < DP[x][y - 1][4]) {
        if (DP[x][y][4]) {
          if (!inQ[x][y - 1]) {
            Q.push({x, y - 1});
            inQ[x][y - 1] = true;
          }
          DP[x][y - 1][4] = DP[x][y][4];
        } else {
          DP[x][y - 1][4] = 1;
        }
      }
    }

    //     DP[i][j][1] = min(DP[i - 1][j][1], DP[i][j][1]);
    //     DP[i][j][1] = min(DP[i - 1][j][3] + 1, DP[i][j][1]);
    //     DP[i][j][1] = min(DP[i - 1][j][4] + 1, DP[i][j][1]);
    //     DP[i][j][2] = min(DP[i + 1][j][2], DP[i][j][2]);
    //     DP[i][j][2] = min(DP[i + 1][j][3] + 1, DP[i][j][2]);
    //     DP[i][j][2] = min(DP[i + 1][j][4] + 1, DP[i][j][2]);
    //     DP[i][j][3] = min(DP[i][j + 1][1] + 1, DP[i][j][3]);
    //     DP[i][j][3] = min(DP[i][j + 1][2] + 1, DP[i][j][3]);
    //     DP[i][j][3] = min(DP[i][j + 1][1] + 1, DP[i][j][3]);
  }
}

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

int main() {
  // freopen("az.in", "r", stdin);
  // freopen("az.out", "w", stdout);
  // input::read(totN), input::read(totM);
  totN = read();
  totM = read();
  for (int i = 1; i <= totN; i++) {
    for (int j = 1; j <= totM; j++) {
      // input::read(s[i][j]);
      s[i][j] = getchar();
      while (s[i][j] != '+' && s[i][j] != '#' && s[i][j] != '.') {
        s[i][j] = getchar();
      }
    }
  }
  memset(ANS, 0x3f, sizeof ANS);
  memset(DP, 0x3f, sizeof DP);
  for (int i = 1; i <= totN; ++i) {
    for (int j = 1; j <= totM; ++j) {
      if (s[i][j] == '+') {
        ++cntSt;
        StX[cntSt] = i;
        StY[cntSt] = j;
        for (int k = 0; k <= 4; ++k) {
          DP[i][j][k] = 0;
        }
      }
    }
  }

  for (int i = 1; i <= cntSt; ++i) {
    FS(StX[i], StY[i]);
  }
  for (int i = 1; i <= totN; ++i) {
    for (int j = 1; j <= totN; ++j) {
      for (int k = 1; k <= 4; ++k) {
        ANS[i][j] = min(ANS[i][j], DP[i][j][k]);
      }
    }
  }
  // for (int i = 1; i <= totN; i++) {
  //   for (int j = 1; j <= totM; j++) {
  //     if (ANS[i][j] == -1) {
  //       output::print('#');
  //     } else if (ANS[i][j] == inf) {
  //       output::print('X');
  //     } else {
  //       // output::print(ANS[i][j]);
  //       write(ANS[i][j]);
  //     }
  //     output::print(" \n"[j == totM]);
  //   }
  // }
  // output::flush();
  for (int i = 1; i <= totN; ++i) {
    for (int j = 1; j <= totM; ++j) {
      if (s[i][j] == '#') {
        putchar('#');
      } else if (ANS[i][j] == INF) {
        putchar('X');
      } else {
        write(ANS[i][j]);
      }
      if (j != totM) {
        putchar(' ');
      }
    }
    putchar('\n');
  }
  return 0;
}
```

然后说正解吧。

其实这个题意思就是朝一个方向走没有价值，转向价值为1。

对空格建 $A, B$ 两份图，$A$ 内部只连横边，$B$ 内部只连竖边，长度都为$0$。
考虑把转弯表示成在 $A, B$ 之间切换。即对每个空格，在 $A, B$ 之间连长度为$1$ 的边。以两图中的每个为
`+` 的点为源，做多源最短路。某空格的答案就是它在 $A, B$ 中对应的两点的最短路取 $min$ 再加$1$ 。
事实上可以用双端队列维护边权为 $0$ 或 $1$ 的最短路（也叫 $01 bfs$）。

不用显式建图，令三元组$(k,i,j)$(k为1或2)表示第$k$ 张图上$(i,j)$ 这个点即可。

```cpp
#include <bits/stdc++.h>
using namespace std;

const int OutputBufferSize = 10000000;
namespace input {
#define BUF_SIZE 100000
#define OUT_SIZE 100000
#define ll long long
bool IOerror = 0;
inline char nc() {
  static char buf[BUF_SIZE], *p1 = buf + BUF_SIZE, *pend = buf + BUF_SIZE;
  if (p1 == pend) {
    p1 = buf;
    pend = buf + fread(buf, 1, BUF_SIZE, stdin);
    if (pend == p1) {
      IOerror = 1;
      return -1;
    }
  }
  return *p1++;
}
inline bool blank(char ch) {
  return ch == ' ' || ch == '\n' || ch == '\r' || ch == '\t';
}
inline void read(char &ch) {
  ch = nc();
  while (blank(ch))
    ch = nc();
}
inline void read(int &x) {
  char ch = nc();
  x = 0;
  for (; blank(ch); ch = nc())
    ;
  if (IOerror)
    return;
  for (; ch >= '0' && ch <= '9'; ch = nc())
    x = x * 10 + ch - '0';
}
#undef ll
#undef OUT_SIZE
#undef BUF_SIZE
}; // namespace input

namespace output {
char buffer[OutputBufferSize];
char *s = buffer;
inline void flush() {
  assert(stdout != NULL);
  fwrite(buffer, 1, s - buffer, stdout);
  s = buffer;
  fflush(stdout);
}
inline void print(const char ch) {
  if (s - buffer > OutputBufferSize - 2)
    flush();
  *s++ = ch;
}
inline void print(char *str) {
  while (*str != 0)
    print(char(*str++));
}
inline void print(int x) {
  char buf[25] = {0}, *p = buf;
  if (x == 0)
    print('0');
  while (x)
    *(++p) = x % 10, x /= 10;
  while (p != buf)
    print(char(*(p--) + '0'));
}
} // namespace output

const long long maxN = 2090;
char s[maxN][maxN];
const int inf = 0x3f3f3f3f;
int n, m, a[maxN][maxN];
bool vis[maxN][maxN];
int moveX[4] = {0, 0, 1, -1};
long long moveY[4] = {1, -1, 0, 0};
queue<pair<int, int>> Q;

int main() {
  input::read(n), input::read(m);
  for (int i = 1; i <= n; i++) {
    for (int j = 1; j <= m; j++) {
      input::read(s[i][j]);
    }
  }

  for (int i = 1; i <= n; i++) {
    for (int j = 1; j <= m; j++) {
      if (s[i][j] == '#') {
        a[i][j] = -1;
        vis[i][j] = 1;
      } else if (s[i][j] == '+') {
        a[i][j] = 0;
        vis[i][j] = 1;
        Q.push(make_pair(i, j));
      } else
        a[i][j] = inf;
    }
  }

  while (!Q.empty()) {
    int x = Q.front().first, y = Q.front().second;
    Q.pop();
    for (int i = 0; i < 4; i++) {
      int Xed = x + moveX[i], Yed = y + moveY[i];
      while (Xed >= 1 && Xed <= n && Yed >= 1 && Yed <= m &&
             (a[Xed][Yed] != -1)) {
        if (vis[Xed][Yed]) {
          Xed += moveX[i];
          Yed += moveY[i];
          continue;
        }
        vis[Xed][Yed] = 1;
        a[Xed][Yed] = a[x][y] + 1;
        Q.push(make_pair(Xed, Yed));
        Xed += moveX[i];
        Yed += moveY[i];
      }
    }
  }

  for (int i = 1; i <= n; i++) {
    for (int j = 1; j <= m; j++) {
      if (a[i][j] == -1) {
        output::print('#');
      } else if (a[i][j] == inf) {
        output::print('X');
      } else {
        output::print(a[i][j]);
      }
      output::print(" \n"[j == m]);
    }
  }
  output::flush();
  return 0;
}
```
## T3

### 题目
西藏可以抽象成一个 $n \times m$ 的网格。行和列从 $0$ 开始编号。我们规定 $(x,y)$ 表示第 $x$ 行第 $y$ 列的格子。然而，并不是所有的格子都是安全的。所有满足 $x\geq y$ 的格子 $(x,y)$ 都是危险的。另外，还有 $k$ 个额外的危险的点，它们会在输入中给出。

吉吉一开始时在 $(0,0)$，现在他要走到 $(n-1,m-1)$。他学过 $\text {OI}$，所以每次只会向右方或下方走（即给 $x$ 或 $y$ $+1$）。吉吉不会经过危险的点。如果一共有 $\alpha$ 种方案，吉吉的开心值就是 $233^{\alpha}$。现在，你想知道他的开心值 $\bmod\ 999911659$ 的值。（$999911659 = 2 \times 3 \times 4679 \times 35617+1$，是一个素数）

### 思路

#### 30分

直接递推。令 $f[i][j]$ 表示从起点走到 $(i,j)$ 的方案数。使用 $f[i][j]=f[i-1][j]+f[i][j-1]$ 递推即可。注意本题求的是 $233^{f[n-1][m-1]}$ 的值，所以我们需要计算的是 $f[n-1][m-1] \bmod 999911658$ 的值，而不是 $f[n-1][m-1] \bmod 999911659$ 的值。时间复杂度 $\Theta(nm)$。

#### 50分

观察到这种网格如果没有额外危险点，从 $(0,0)$ 走到 $(n-1,n-1)$ 的方案数为卡特兰数的第 $n-1$ 项。而我们要计算的是答案 $\bmod  999911658 = 2 \times 3 \times 4679 \times 35617$ 的结果，所以只需对每个素因子分别使用 $\text {Lucas}$ 计算组合数，然后将答案使用中国剩余定理合并即可。

#### 70分
Subtask #3：$k=0 \ (20 \ pts)$
这个数据点要求我们理解卡特兰数的本质。当然如果你在 $\text C$ 班好好听讲也可以作出这个点。我们考虑从 $(x_0, y_0)$  走到 $(x_1, y_1)$ 的方案数。我们考虑先计算出没有 $x\geq y$ 不能走的限制的方案数，再减去不合法的方案数。总方案数显然是：
$$\binom{x_1-x_0+y_1-y_0}{x_1-x_0}$$
对于不合法的方案，我们考虑这个路径。它一定穿过一条 $x = y$ 的直线。也就是说，他一定与这条直线有交点。
```
*-*-*-*-*-*-*
 \| | | | | |
* *-*-*-*-*-*
   \| | | | |
* * *-*-*-*-*
     \| | | |
* * * *-*-*-*
       \| | |
* * * * *-*-*
```
我们考虑将这条路径和这条直线的第一个交点取出，然后将这条路径中这个交点之前的路径全部以这条直线为对称轴翻转过来。这样，每条路径就队对应了从一个新的起点（原起点以直线为对称轴后翻转得到的点）和原终点的所有路径条数。这样的路径条数为：

$$\binom{x_1-x_0+y_1-y_0}{y_1-y_0+(y_0-x_0+1)}$$

于是我们就可以用刚才 50分做法 中介绍的的技巧算出这个值了。结合前述算法可得 $70 \ pts$。

#### 奇技淫巧

由于数据随机，并且最大模数只有 $35617$，所以组合数到一定范围 $\bmod 35617$ 就等于 $0$ 了。于是我们只需要输出 $233^0=1$ 即可获得 $40 \ pts$。

#### 满分

我们考虑容斥。先令从 $(x_0, y_0)$  走到 $(x_1, y_1)$ 的方案数为 $paths(x_0, y_0, x_1, y_1)$。令 $f[i]$ 表示到达第 $i$ 个危险点，并且不到达其他的危险点的总方案数。转移方程：$f[i]=paths(0,0,x_i,y_i)-\sum_{x_j\le x_i, y_j\le y_i}paths(x_j, y_j, x_i, y_i)$。于是，我们就可以在 $\Theta(k^2)$ 的时间内解决这个问题了。

这个题因为涉及很多数学，我只是能理解思路，但是并不会实现，暂时不写代码了。