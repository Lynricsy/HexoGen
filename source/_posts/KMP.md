---
title: KMP算法
date: 2021-05-02
tags:
 - 字符串
categories: 字符串
description: 关于一种简单的基础字符串匹配算法——KMP算法。
cover: https://cdn.jsdelivr.net/gh/thomitics/picbed/pics/%E7%8E%89%E6%89%89%E7%BB%95%E5%B0%98%E6%AD%8C-2048x1536.jpg
---
## 前置知识：字符串前缀树组

## 前缀函数定义

给定一个长度为 $n$ 的字符串 $s$，其 **前缀函数** 被定义为一个长度为 $n$ 的数组 $\pi$。
其中 $\pi[i]$ 的定义是：

1. 如果子串 $s[0\dots i]$ 有一对相等的真前缀与真后缀：$s[0\dots k-1]$ 和 $s[i - (k - 1) \dots i]$，那么 $\pi[i]$ 就是这个相等的真前缀（或者真后缀，因为它们相等：)）的长度，也就是 $\pi[i]=k$；
2. 如果不止有一对相等的，那么 $\pi[i]$ 就是其中最长的那一对的长度；
3. 如果没有相等的，那么 $\pi[i]=0$。

简单来说 $\pi[i]$ 就是，子串 $s[0\dots i]$ 最长的相等的真前缀与真后缀的长度。

用数学语言描述如下：

$$
\pi[i] = \max_{k = 0 \dots i}\{k: s[0 \dots k - 1] = s[i - (k - 1) \dots i]\}
$$

特别地，规定 $\pi[0]=0$。

举例来说，对于字符串 `abcabcd`，

$\pi[0]=0$，因为 `a` 没有真前缀和真后缀，根据规定为 0

$\pi[1]=0$，因为 `ab` 无相等的真前缀和真后缀

$\pi[2]=0$，因为 `abc` 无相等的真前缀和真后缀

$\pi[3]=1$，因为 `abca` 只有一对相等的真前缀和真后缀：`a`，长度为 1

$\pi[4]=2$，因为 `abcab` 相等的真前缀和真后缀只有 `ab`，长度为 2

$\pi[5]=3$，因为 `abcabc` 相等的真前缀和真后缀只有 `abc`，长度为 3

$\pi[6]=0$，因为 `abcabcd` 无相等的真前缀和真后缀

同理可以计算字符串 `aabaaab` 的前缀函数为 $[0, 1, 0, 1, 2, 2, 3]$。

## 计算前缀函数的朴素算法

一个直接按照定义计算前缀函数的算法流程：

- 在一个循环中以 $i = 1\to n - 1$ 的顺序计算前缀函数 $\pi[i]$ 的值（$\pi[0]$ 被赋值为 $0$）。
- 为了计算当前的前缀函数值 $\pi[i]$，我们令变量 $j$ 从最大的真前缀长度 $i$ 开始尝试。
- 如果当前长度下真前缀和真后缀相等，则此时长度为 $\pi[i]$，否则令 j 自减 1，继续匹配，直到 $j=0$。
- 如果 $j = 0$ 并且仍没有任何一次匹配，则置 $\pi[i] = 0$ 并移至下一个下标 $i + 1$。

具体实现如下：

```cpp
vector<int> prefix_function(string s) {
  int n = (int)s.length();
  vector<int> pi(n);
  for (int i = 1; i < n; i++)
    for (int j = i; j >= 0; j--)
      if (s.substr(0, j) == s.substr(i - j + 1, j)) {
        pi[i] = j;
        break;
      }
  return pi;
}
```

注：

- `string substr (size_t pos = 0, size_t len = npos) const;`

显见该算法的时间复杂度为 $O(n^3)$，具有很大的改进空间。

## 计算前缀函数的高效算法

### 第一个优化

第一个重要的观察是 **相邻的前缀函数值至多增加 $1$**。

参照下图所示，只需如此考虑：当取一个尽可能大的 $\pi[i+1]$ 时，必然要求新增的 $s[i+1]$ 也与之对应的字符匹配，即 $s[i+1]=s[\pi[i]]$, 此时 $\pi[i+1] = \pi[i]+1$。


$\underbrace{\overbrace{s_0 ~ s_1 ~ s_2}^{\pi[i] = 3} ~ s_3}_{\pi[i+1] = 4} ~ \dots ~ \underbrace{\overbrace{s_{i-2} ~ s_{i-1} ~ s_{i}}^{\pi[i] = 3} ~ s_{i+1}}_{\pi[i+1] = 4}
$


所以当移动到下一个位置时，前缀函数的值要么增加一，要么维持不变，要么减少。

此时的改进的算法为：

```cpp
vector<int> prefix_function(string s) {
  int n = (int)s.length();
  vector<int> pi(n);
  for (int i = 1; i < n; i++)
    for (int j = pi[i - 1] + 1; j >= 0; j--)  // improved: j=i => j=pi[i-1]+1
      if (s.substr(0, j) == s.substr(i - j + 1, j)) {
        pi[i] = j;
        break;
      }
  return pi;
}
```

在这个初步改进的算法中，在计算每个 $\pi[i]$ 时，最好的情况是第一次字符串比较就完成了匹配，也就是说基础的字符串比较次数是 `n-1` 次。

而由于存在 `j = pi[i-1]+1`（`pi[0]=0`）对于最大字符串比较次数的限制，可以看出每次只有在最好情况才会为字符串比较次数的上限积累 1，而每次超过一次的字符串比较消耗的是之后次数的增长空间。

由此我们可以得出字符串比较次数最多的一种情况：至少 `1` 次字符串比较次数的消耗和最多 `n-2` 次比较次数的积累，此时字符串比较次数为 `n-1 + n-2 = 2n-3`。

可见经过此次优化，计算前缀函数只需要进行 $O(n)$ 次字符串比较，总复杂度降为了 $O(n^2)$。

### 第二个优化

在第一个优化中，我们讨论了计算 $\pi[i+1]$ 时的最好情况：$s[i+1]=s[\pi[i]]$，此时 $\pi[i+1] = \pi[i]+1$。现在让我们沿着这个思路走得更远一点：讨论当 $s[i+1] \neq s[\pi[i]]$ 时如何跳转。

失配时，我们希望找到对于子串 $s[0\dots i]$，仅次于 $\pi[i]$ 的第二长度 $j$，使得在位置 $i$ 的前缀性质仍得以保持，也即 $s[0 \dots j - 1] = s[i - j + 1 \dots i]$：

$$
\overbrace{\underbrace{s_0 ~ s_1}_j ~ s_2 ~ s_3}^{\pi[i]} ~ \dots ~ \overbrace{s_{i-3} ~ s_{i-2} ~ \underbrace{s_{i-1} ~ s_{i}}_j}^{\pi[i]} ~ s_{i+1}
$$

如果我们找到了这样的长度 $j$，那么仅需要再次比较 $s[i + 1]$ 和 $s[j]$。如果它们相等，那么就有 $\pi[i + 1] = j + 1$。否则，我们需要找到子串 $s[0\dots i]$ 仅次于 $j$ 的第二长度 $j^{(2)}$，使得前缀性质得以保持，如此反复，直到 $j = 0$。如果 $s[i + 1] \neq s[0]$，则 $\pi[i + 1] = 0$。

观察上图可以发现，因为 $s[0\dots \pi[i]-1] = s[i-\pi[i]+1\dots i]$，所以对于 $s[0\dots i]$ 的第二长度 $j$，有这样的性质：

$$
s[0 \dots j - 1] = s[i - j + 1 \dots i]= s[\pi[i]-j\dots \pi[i]-1]
$$

也就是说 $j$ 等价于子串 $s[\pi[i]-1]$ 的前缀函数值，即 $j=\pi[\pi[i]-1]$。同理，次于 $j$ 的第二长度等价于 $s[j-1]$ 的前缀函数值，$j^{(2)}=\pi[j-1]$

显然我们可以得到一个关于 $j$ 的状态转移方程：$j^{(n)}=\pi[j^{(n-1)}-1], \ \ (j^{(n-1)}>0)$

### 最终算法

所以最终我们可以构建一个不需要进行任何字符串比较，并且只进行 $O(n)$ 次操作的算法。

而且该算法的实现出人意料的短且直观：

```cpp
vector<int> prefix_function(string s) {
  int n = (int)s.length();
  vector<int> pi(n);
  for (int i = 1; i < n; i++) {
    int j = pi[i - 1];
    while (j > 0 && s[i] != s[j]) j = pi[j - 1];
    if (s[i] == s[j]) j++;
    pi[i] = j;
  }
  return pi;
}
```

这是一个 **在线** 算法，即其当数据到达时处理它——举例来说，你可以一个字符一个字符的读取字符串，立即处理它们以计算出每个字符的前缀函数值。该算法仍然需要存储字符串本身以及先前计算过的前缀函数值，但如果我们已经预先知道该字符串前缀函数的最大可能取值 $M$，那么我们仅需要存储该字符串的前 $M + 1$ 个字符以及对应的前缀函数值。
## KMP算法
首先，默认大家字符串匹配的暴力算法（称作`Brute-Force`）是已经会了的。
它最坏的情况如下图所示：
![](https://cdn.jsdelivr.net/gh/thomitics/picbed/pics/v2-4fe5612ff13a6286e1a8e50a0b06cd96_r.jpg)
我们很难降低字符串比较的复杂度（因为比较两个字符串，真的只能逐个比较字符）。因此，我们考虑降低比较的趟数。如果比较的趟数能降到足够低，那么总的复杂度也将会下降很多。　　要优化一个算法，首先要回答的问题是“我手上有什么信息？”　我们手上的信息是否足够、是否有效，决定了我们能把算法优化到何种程度。请记住：**尽可能利用残余的信息，是KMP算法的思想所在**。

　　在 Brute-Force 中，如果从 $S[i]$ 开始的那一趟比较失败了，算法会直接开始尝试从 $S[i+1]$ 开始比较。这种行为，属于典型的“没有从之前的错误中学到东西”。我们应当注意到，一次失败的匹配，会给我们提供宝贵的信息——如果 $S[i : i+len(P)]$ 与 $P$ 的匹配是在第 `r` 个位置失败的，那么从 $S[i]$ 开始的 `(r-1)` 个连续字符，一定与 P 的前 `(r-1)` 个字符一模一样！

![](https://cdn.jsdelivr.net/gh/thomitics/picbed/pics/v2-7dc61b0836af61e302d9474eeeecfe83_r.jpg)
需要实现的任务是“字符串匹配”，而每一次失败都会给我们换来一些信息——能告诉我们，主串的某一个子串等于模式串的某一个前缀。但是这又有什么用呢？
## 跳过不可能成功的字符串比较
　　有些趟字符串比较是有可能会成功的；有些则毫无可能。我们刚刚提到过，优化 `Brute-Force` 的路线是“尽量减少比较的趟数”，而如果我们跳过那些绝不可能成功的字符串比较，则可以希望复杂度降低到能接受的范围。

　　那么，哪些字符串比较是不可能成功的？来看一个例子。已知信息如下：

模式串 P = "abcabd".
和主串从$S[0]$开始匹配时，在 $P[5]$ 处失配。

![](https://cdn.jsdelivr.net/gh/thomitics/picbed/pics/v2-372dc6c567ba53a1e4559fdb0cb6b206_r.jpg)


　　首先，利用上一节的结论。既然是在 P[5] 失配的，那么说明 S[0:5] 等于 P[0:5]，即"abcab". 现在我们来考虑：从 S[1]、S[2]、S[3] 开始的匹配尝试，有没有可能成功？
　　从 S[1] 开始肯定没办法成功，因为 S[1] = P[1] = 'b'，和 P[0] 并不相等。从 S[2] 开始也是没戏的，因为 S[2] = P[2] = 'c'，并不等于P[0]. 但是从 S[3] 开始是有可能成功的——至少按照已知的信息，我们推不出矛盾。

![](https://cdn.jsdelivr.net/gh/thomitics/picbed/pics/v2-67dd66b86323d3d08f976589cf712a1a_r.jpg)

## next数组

　　next数组是对于模式串而言的。P 的 next 数组定义为：next[i] 表示 P[0] ~ P[i] 这一个子串，使得 前k个字符恰等于后k个字符 的最大的k. 特别地，k不能取i+1（因为这个子串一共才 i+1 个字符，自己肯定与自己相等，就没有意义了）。

![](https://cdn.jsdelivr.net/gh/thomitics/picbed/pics/v2-49c7168b5184cc1744459f325e426a4a_r.jpg)

上图给出了一个例子。P="abcabd"时，next[4]=2，这是因为P[0] ~ P[4] 这个子串是"abcab"，前两个字符与后两个字符相等，因此next[4]取2. 而next[5]=0，是因为"abcabd"找不到前缀与后缀相同，因此只能取0.

　　如果把模式串视为一把标尺，在主串上移动，那么 Brute-Force 就是每次失配之后只右移一位；改进算法则是每次失配之后，移很多位，跳过那些不可能匹配成功的位置。但是该如何确定要移多少位呢？

![](https://cdn.jsdelivr.net/gh/thomitics/picbed/pics/v2-d6c6d433813595dce5aad08b40dc0b72_r.jpg)

在 S[0] 尝试匹配，失配于 S[3] <=> P[3] 之后，我们直接把模式串往右移了两位，让 S[3] 对准 P[1]. 接着继续匹配，失配于 S[8] <=> P[6], 接下来我们把 P 往右平移了三位，把 S[8] 对准 P[3]. 此后继续匹配直到成功。
　　我们应该如何移动这把标尺？很明显，如图中蓝色箭头所示，旧的后缀要与新的前缀一致（如果不一致，那就肯定没法匹配上了）！


　　回忆next数组的性质：P[0] 到 P[i] 这一段子串中，前next[i]个字符与后next[i]个字符一模一样。既然如此，如果失配在 P[r], 那么P[0]~P[r-1]这一段里面，前next[r-1]个字符恰好和后next[r-1]个字符相等——也就是说，我们可以拿长度为 next[r-1] 的那一段前缀，来顶替当前后缀的位置，让匹配继续下去！
　　您可以验证一下上面的匹配例子：P[3]失配后，把P[next[3-1]]也就是P[1]对准了主串刚刚失配的那一位；P[6]失配后，把P[next[6-1]]也就是P[3]对准了主串刚刚失配的那一位。

![](https://cdn.jsdelivr.net/gh/thomitics/picbed/pics/v2-6ddb50d021e9fa660b5add8ea225383a_r.jpg)

如上图所示，绿色部分是成功匹配，失配于红色部分。深绿色手绘线条标出了相等的前缀和后缀，其长度为next[右端]. 由于手绘线条部分的字符是一样的，所以直接把前面那条移到后面那条的位置。因此说，next数组为我们如何移动标尺提供了依据。接下来，我们实现这个优化的算法。

而next数组的求法，我们前面已经讲过了，这里就不在赘述。

## 小练习

- [UVA 455 "Periodic Strings"](http://uva.onlinejudge.org/index.php?option=onlinejudge&page=show_problem&problem=396)
- [UVA 11022 "String Factoring"](http://uva.onlinejudge.org/index.php?option=onlinejudge&page=show_problem&problem=1963)
- [UVA 11452 "Dancing the Cheeky-Cheeky"](http://uva.onlinejudge.org/index.php?option=onlinejudge&page=show_problem&problem=2447)
- [UVA 12604 - Caesar Cipher](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=4282)
- [UVA 12467 - Secret Word](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=3911)
- [UVA 11019 - Matrix Matcher](https://uva.onlinejudge.org/index.php?option=onlinejudge&page=show_problem&problem=1960)
- [SPOJ - Pattern Find](http://www.spoj.com/problems/NAJPF/)
- [Codeforces - Anthem of Berland](http://codeforces.com/contest/808/problem/G)
- [Codeforces - MUH and Cube Walls](http://codeforces.com/problemset/problem/471/D)

好耶ヾ(✿ﾟ▽ﾟ)ノ！！！
