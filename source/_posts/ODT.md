---
title: 珂朵莉树
date: 2021-05-01
tags:
 - 数据结构
categories: 数据结构
description: 关于一种暴力数据结构——珂朵莉树！
cover: https://www.z4a.net/images/2021/04/29/43c1b40837a8d0f9245554bac363fe1a7a21a74b.png
---
<h2> 引：CF896C </h2>
<a href="https://codeforces.com/problemset/problem/896/C"> 原题在这里</a>

### 题目
请你写一种奇怪的数据结构，支持：
- $1$  $l$  $r$  $x$ ：将$[l,r]$ 区间所有数加上$x$ 
- $2$  $l$  $r$  $x$ ：将$[l,r]$ 区间所有数改成$x$ 
- $3$  $l$  $r$  $x$ ：输出将$[l,r]$ 区间从小到大排序后的第$x$ 个数是的多少(即区间第$x$ 小，数字大小相同算多次，保证 $1\leq$  $x$  $\leq$  $r-l+1$  )
- $4$  $l$  $r$  $x$  $y$ ：输出$[l,r]$ 区间每个数字的$x$ 次方的和模$y$ 的值(即($\sum^r_{i=l}a_i^x$ ) $\mod y$ )

### 输入格式
这道题目的输入格式比较特殊，需要选手通过$seed$ 自己生成输入数据。
输入一行四个整数$n,m,seed,v_{max}$ （$1\leq $  $n,m\leq 10^{5}$  ,$0\leq seed \leq 10^{9}+7$  $,1\leq vmax \leq 10^{9} $ ）
其中$n$ 表示数列长度，$m$ 表示操作次数，后面两个用于生成输入数据。

其中上面的op指题面中提到的四个操作。

### 输出格式
对于每个操作3和4，输出一行仅一个数。</div>

### 数据
本体的数据由以下代码生成：
```python
def rnd():

    ret = seed
    seed = (seed * 7 + 13) mod 1000000007
    return ret

for i = 1 to n:

    a[i] = (rnd() mod vmax) + 1

for i = 1 to m:

    op = (rnd() mod 4) + 1
    l = (rnd() mod n) + 1
    r = (rnd() mod n) + 1

    if (l > r):
         swap(l, r)

    if (op == 3):
        x = (rnd() mod (r - l + 1)) + 1
    else:
        x = (rnd() mod vmax) + 1

    if (op == 4):
        y = (rnd() mod vmax) + 1
```
## 珂朵莉树
通过我们看上面的题，可以发现它最大的一个特点：</br>
数据随机</br>
那么，这有什么用呢？</br>
自然就是给一些复杂度 $O(\text{玄学})$的算法使用了。<br>
于是，我们的珂朵莉树就来了。

## 这是什么？
珂朵莉树的思想在于随机数据下的区间赋值操作很可能让大量元素变为同一个数。所以我们以三元组<l,r,v>的形式保存数据（区间 $[l,r]$ 中的元素的值都是v）。而他的功能就只有一个：区间赋值。

## 用途
骗分。只要是有区间赋值操作的数据结构题都可以用来骗分。在数据随机的情况下一般效率较高，但在不保证数据随机的场合下，会被精心构造的特殊数据卡到超时。</p> <p>如果要保证复杂度正确，必须保证数据随机。详见 <a href="http://codeforces.com/blog/entry/56135?#comment-398940">Codeforces 上关于珂朵莉树的复杂度的证明</a>。</p> <p>更详细的严格证明见 <a href="https://zhuanlan.zhihu.com/p/102786071">珂朵莉树的复杂度分析</a>。对于 add，assign 和 sum 操作，用 set 实现的珂朵莉树的复杂度为$O(n \log \log n)$

## 详解

### Node
根据定义，节点就是这样：

![image.png](https://i.loli.net/2021/04/29/VkLTgmB7WFGYs8I.png)

所以，节点是这样定义的：

```cpp
struct Node
{
	int l, r;
	mutable long long val;
	Node(int L, int R = -1, int w = 0)
	{
		l = L;
		r = R;
		val = w;
	}
};
```
`mutable`的意思是“可变的”，让我们可以在后面的操作中修改 v 的值。在 `C++` 中，`mutable` 是为了突破 `const` 的限制而设置的。被 `mutable` 修饰的变量（`mutable` 只能用于修饰类中的非静态数据成员），将永远处于可变的状态，即使在一个 `const` 函数中。
这意味着，我们可以直接修改已经插入 `set` 的元素的 $v$值，而不用将该元素取出后重新加入 `set`。

存到`set`中：
```cpp
set<Node> ODT;
```
### split
`split` 是最核心的操作之一，它用于将原本包含点 $x$的区间（设为$[l,r]$ ）分裂为两个区间$[l,x)$和$[x,r]$  并返回指向后者的迭代器。 代码如下：

```cpp
auto split(int pos)
{
	auto it = ODT.lower_bound(Node(pos));
	if (it != ODT.end() && it->l == pos)
	{
		return it;
	}
	--it;
	int L = it->l;
	int R = it->r;
	int w = it->val;
	ODT.erase(it);
	ODT.insert(Node(L, pos - 1, w));
	return ODT.insert(Node(pos, R, w)).first;
}
```
用图片演示一下：
比如`split(4)`:
<img src="https://www.z4a.net/images/2021/04/29/v2-e2bfb654e0549283734097606845b695_r.png">

首先`lower_bound`，找到左端点大于等于$4$的节点，`<5,6,3>`。它的左端点不是4，所以回退，得`<2,4,2>`。我们把节点`<2,4,2>`删除，然后插入`<2,3,2>`及`<4,4,2>`即可。是不是很简单？

<img src="https://www.z4a.net/images/2021/04/29/v2-f8d3b687974d1a054e0812acd58d5626_r.png">

### 区间赋值：
珂朵莉树的精髓在于区间赋值。而区间赋值操作的写法也极其简单：

```cpp
void assign(int L, int R, int w)
{
	auto initL = split(L);
	auto initR = split(R + 1);
	ODT.erase(initL, initR);
	ODT.insert(Node(L, R, w));
}
```

好了，基础操作就这些。是不是很简单？

## 回CF896C

好了，我们已经会了这个数据结构，那让我们水掉这道题吧？

### 区间k大值

```cpp
ll kth(ll l, ll r, ll k)
{
    auto end = split(r + 1);
    vector<pair<ll, ll>> v; // 这个pair里存节点的值和区间长度
    for (auto it = split(l); it != end; it++)
        v.push_back(make_pair(it->v, it->r - it->l + 1));
    sort(v.begin(), v.end()); // 直接按节点的值的大小排下序
    for (int i = 0; i < v.size(); i++) // 然后挨个丢出来，直到丢出k个元素为止
    {
        k -= v[i].second;
        if (k <= 0)
            return v[i].first;
    }
}
```
### 区间n次方和
```cpp
ll sum_of_pow(ll l, ll r, ll x, ll y)
{
    ll tot = 0;
    auto end = split(r + 1);
    for (auto it = split(l); it != end; it++)
        tot = (tot + qpow(it->v, x, y) * (it->r - it->l + 1)) % y; // qpow自己写一下
    return tot;
}
```
估计你也发现了，珂朵莉树上的操作，
<center><h2>全 · 是 · 暴 · 力</h2></center>
！！！

好了，就这些，是不是很简单呢？

## 课后小练习

- [「SCOI2010」序列操作](https://www.luogu.com.cn/problem/P2572)
- [「SHOI2015」脑洞治疗仪](https://loj.ac/problem/2037)
- [「Luogu 2787」理理思维](https://www.luogu.com.cn/problem/P2787)
- [「Luogu 4979」矿洞：坍塌](https://www.luogu.com.cn/problem/P4979)

好耶ヾ(✿ﾟ▽ﾟ)ノ
