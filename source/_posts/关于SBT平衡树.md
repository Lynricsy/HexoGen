---
title: 关于SBT平衡树
date: 2021-01-15 10:21:47
top_img: https://api.r10086.com/%E9%A3%8E%E6%99%AF%E7%B3%BB%E5%88%9710.php
cover: https://tva1.sinaimg.cn/large/9bd9b167ly1fwsfzt2p29j21hc0u04qp.jpg  
tags:
- 数据结构
categories: 数据结构 
description: SBT平衡树学习历程与详解。
keywords:
- SBT
- 平衡树
---
现在我发现写各种数据结构用指针的好少啊……所以我想写一个指针版的SBT</br>
明明指针版的又好理解又好康又好写又好记啊对吧
<h2>迷惑行为</h2>
<p class='div-border green'>
一开始学平衡树的时候学的是替罪羊树……然后后来替罪羊树就愣是很久都没打出来……（虽然替罪羊树qs好写）
后看书的时候各种书上都没有替罪羊树（可能是因为名字就不太✨？）<br/>
又不能浪费看书的时间所以就转投了书上有的SBT。
</p>
<h2>迷惑行为++</h2>
第二天上机的时候又忘了一部分就看着别人的写
然后就看到这样一份代码：

```cpp
int remove(int &x,int key)
{
	int d_key;
	tree[x].size--;
	if ((key == tree[x].key) || (key < tree[x].key && tree[x].left == 0) ||
	    (key > tree[x].key && tree[x].right == 0))
	{
		d_key = tree[x].key;
		if (tree[x].left && tree[x].right)
		{
			tree[x].key = remove(tree[x].left, tree[x].key + 1);
		} else
		{
			x = tree[x].left + tree[x].right;
		}
	} else if (key > tree[x].key)
		d_key = remove(tree[x].right, key);
	else if (key < tree[x].key)
		d_key = remove(tree[x].left, key);
	return d_key;
}
```
看到这一行了没有：

```cpp
x = tree[x].left + tree[x].right;
```
感觉好迷惑<br/>
两个指针相加……<br/>
总之是不知道想干什么

热心的`JYC`小同学热心的指出了热心的问题<br/>
前面有一句这个

```cpp
if (tree[x].left && tree[x].right)
```

也就是说，这两个`left`,`right`至少有一个是零<br/>
这一句的值就是非零的那个……<br/>
是我太菜了<br/>


<h2>MODULE解析</h2>

<h3>定义Node</h3>

```cpp
struct Node
{
	Node *lch, *rch;//左儿子，右儿子
	int val, size;//键值，字数
	Node(int key)//初始化
    {
	    lch=rch=NULL;
	    size=1;
	    val=key;
    }
}rot(0);
```
<h3>左旋，右旋</h3>

```c++
void L_rotate(Node *x)
{
	Node *y = x->lch;
	x->rch = y->lch;
	y->lch = x;
	y->size = x->size;
	x->size = x->lch->size + x->rch->size + 1;
	x = y;
}
void R_rotate(Node *x)
{
	Node *y = x->lch;
	x->lch = y->rch;
	y->rch = x;
	y->size = x->size;
	x->size = x->lch->size + x->rch->size + 1;
	x = y;
}
```
<h3>maintain</h3>

```c++
void maintain(Node *x,bool flag)
{
    if (flag== false)
    {
        if (x->lch->lch->size>x->rch->size)
        {
            R_rotate(x);
        } else if (x->lch->rch->size>x->rch->size)
        {
            R_rotate(x->lch);
            R_rotate(x);
        } else
        {
            return;
        }
    } else
    {
        if (x->rch->rch->size>x->lch->size)
        {
            L_rotate(x);
        } else if (x->rch->lch->size>x->lch->size)
        {
            R_rotate(x->rch);
            R_rotate(x);
        } else
        {
            return;
        }
    }
    maintain(x->lch, false);
    maintain(x->rch, true);
    maintain(x, true);
    maintain(x, false);
}
```
<h3>插♂入</h3>

```c++
void insert(Node *x,int keys)
{
	if (x == NULL)
	{
		x = new Node(keys);
	} else
	{
		x->size++;
		if (keys < x->val)
		{
			insert(x->lch, keys);
		} else
		{
			insert(x->rch, keys);
		}
		maintain(x, keys >= x->val);
	}
}
```
<h3>删除</h3>

```c++
int remove(Node *x,int keys)
{
	int dekey;
	--x->size;
	if ((keys==x->val)||(keys<x->val&&x->lch==NULL)||(keys>x->val&&x->rch==NULL))
	{
		dekey=x->val;
		if (x->lch&&x->rch)
		{
			x->val=remove(x->lch,x->val+1);
		} else
		{
			if (x->lch==NULL)
			{
				x=x->rch;
			} else if (x->rch==NULL)
			{
				x=x->lch;
			} else
			{
				x=NULL;
			}
		}
	} else if (keys>x->val)
	{
		dekey=remove(x->rch,keys);
	} else if (keys<x->val)
	{
		dekey=remove(x->rch,keys);
	}
	return dekey;
}
```
<h3>查找最小值</h3>

```c++
int get_min()
{
	Node *x;
	for (x = &rot; x->lch; x=x->lch);
	return x->val;
}
```
<h3>查找最大值</h3>

```c++
int get_max()
{
	Node *x;
	for (x = &rot; x->rch; x=x->rch);
	return x->val;
}
```
<h3>查询第K大的值</h3>

```c++
int find_Kth(Node *x,int K)
{
	int tr=x->lch->size+1;
	if (tr==K)
	{
		return x->val;
	} else if (tr<K)
	{
		return find_Kth(x->rch,K-tr);
	} else
	{
		return find_Kth(x->lch,K);
	}
}
```
<h3>查询值的排名</h3>

```c++
int rank(Node *x,int keys)
{
	if (keys<x->val)
	{
		return rank(x->lch,keys);
	} else if (keys>x->val)
	{
		return rank(x->rch,keys);
	}
	return x->lch->size+1;
}
```
<h3>查询前驱</h3>

```c++
int Lowwer(int w)
{
	Node *now=rot;
	int result=-2147483600;
	while(now)
	{
		if(now->val<w&&now->val>result)
		{
			result=now->val;
		}
		if(w>now->val)
		{
			now=now->rch;
		}
		else
		{
			now=now->lch;
		}
	}
	return result;
}
```

<h3>查询后继</h3>

```c++
int Upper(int w)
{
	Node *now=rot;
	int result=2147483600;
	while(now)
	{
		if(now->val>w&&now->val<result)
		{
			result=now->val;
		}
		if(w<now->val)
		{
			now=now->lch;
		}
		else
		{
			now=now->rch;
		}
	}
	return result;
}
```

<h3>main</h3>

```c++
int main()
{
	totN=read();
	register char opt;
	register long long K;
	for(register long long i=1;i<=totN;++i)
	{
		opt=getchar();
		K=read();
		switch (opt)
		{
		case '1':
			insert(rot,K);
			break;
		case '2':
			remove(rot,K);
			break;
		case '3':
			write(ranking(rot,K));
		case '4':
			write(find_Kth(rot,K));
		case '5':
			write(Lowwer(K));
		case '6':
			write(Upper(K));
		default:
			break;
		}
	}
	return 0;
} // LikiBlaze Code
```

<h2>完整代码（整洁，不压行，代码界<s>清流(WWF狂喜)</s>)(368行预警)</h2>

```c++
#include <bits/stdc++.h>

using namespace std;

inline long long read()
{
	long long x = 0;
	int f = 1;
	char ch = getchar();
	while (ch < '0' || ch > '9')
	{
		if (ch == '-')
			f = -1;
		ch = getchar();
	}
	while (ch >= '0' && ch <= '9')
	{
		x = (x << 1) + (x << 3) + (ch ^ 48);
		ch = getchar();
	}
	return x * f;
}
void write(const long long &x)
{
	if (!x)
	{
		putchar('0');
		return;
	}
	char f[100];
	long long tmp = x;
	if (tmp < 0)
	{
		tmp = -tmp;
		putchar('-');
	}
	long long s = 0;
	while (tmp > 0)
	{
		f[s++] = tmp % 10 + '0';
		tmp /= 10;
	}
	while (s > 0)
	{
		putchar(f[--s]);
	}
}
inline double dread()
{
	double r;
	double x = 0, t = 0;
	int s = 0, f = 1;
	char c = getchar();
	for (; !isdigit(c); c = getchar())
	{
		if (c == '-')
		{
			f = -1;
		}
		if (c == '.')
		{
			goto readt;
		}
	}
	for (; isdigit(c) && c != '.'; c = getchar())
	{
		x = x * 10 + c - '0';
	}
readt:
	for (; c == '.'; c = getchar())
		;
	for (; isdigit(c); c = getchar())
	{
		t = t * 10 + c - '0';
		++s;
	}
	r = (x + t / pow(10, s)) * f;
	return r;
}

inline void dwrite(long long x)
{
	if (x == 0)
	{
		putchar(48);
		return;
	}
	int bit[20], p = 0, i;
	for (; x; x /= 10)
		bit[++p] = x % 10;
	for (i = p; i > 0; --i)
		putchar(bit[i] + 48);
}
inline void write(double x, int k)
{
	static int n = pow(10, k);
	if (x == 0)
	{
		putchar('0');
		putchar('.');
		for (int i = 1; i <= k; ++i)
			putchar('0');
		return;
	}
	if (x < 0)
		putchar('-'), x = -x;
	long long y = (long long)(x * n) % n;
	x = (long long)x;
	dwrite(x), putchar('.');
	int bit[10], p = 0, i;
	for (; p < k; y /= 10)
		bit[++p] = y % 10;
	for (i = p; i > 0; i--)
		putchar(bit[i] + 48);
}

struct Node
{
	Node *lch, *rch;
	int val, size;
	Node(int key)
	{
		lch = rch = NULL;
		size = 1;
		val = key;
	}
};

Node *rot = new Node(0);

void L_rotate(Node *x)
{
	Node *y = x->lch;
	x->rch = y->lch;
	y->lch = x;
	y->size = x->size;
	x->size = x->lch->size + x->rch->size + 1;
	x = y;
}

void R_rotate(Node *x)
{
	Node *y = x->lch;
	x->lch = y->rch;
	y->rch = x;
	y->size = x->size;
	x->size = x->lch->size + x->rch->size + 1;
	x = y;
}

void maintain(Node *x, bool flag)
{
	if (flag == false)
	{
		if (x->lch->lch->size > x->rch->size)
		{
			R_rotate(x);
		}
		else if (x->lch->rch->size > x->rch->size)
		{
			R_rotate(x->lch);
			R_rotate(x);
		}
		else
		{
			return;
		}
	}
	else
	{
		if (x->rch->rch->size > x->lch->size)
		{
			L_rotate(x);
		}
		else if (x->rch->lch->size > x->lch->size)
		{
			R_rotate(x->rch);
			R_rotate(x);
		}
		else
		{
			return;
		}
	}
	maintain(x->lch, false);
	maintain(x->rch, true);
	maintain(x, true);
	maintain(x, false);
}

void insert(Node *x, int keys)
{
	if (x == NULL)
	{
		x = new Node(keys);
	}
	else
	{
		x->size++;
		if (keys < x->val)
		{
			insert(x->lch, keys);
		}
		else
		{
			insert(x->rch, keys);
		}
		maintain(x, keys >= x->val);
	}
}

int remove(Node *x, int keys)
{
	int dekey;
	--x->size;
	if ((keys == x->val) || (keys < x->val && x->lch == NULL) || (keys > x->val && x->rch == NULL))
	{
		dekey = x->val;
		if (x->lch && x->rch)
		{
			x->val = remove(x->lch, x->val + 1);
		}
		else
		{
			if (x->lch == NULL)
			{
				x = x->rch;
			}
			else if (x->rch == NULL)
			{
				x = x->lch;
			}
			else
			{
				x = NULL;
			}
		}
	}
	else if (keys > x->val)
	{
		dekey = remove(x->rch, keys);
	}
	else if (keys < x->val)
	{
		dekey = remove(x->rch, keys);
	}
	return dekey;
}

int get_min()
{
	Node *x;
	for (x = rot; x->lch; x = x->lch)
		;
	return x->val;
}
int get_max()
{
	Node *x;
	for (x = rot; x->rch; x = x->rch)
		;
	return x->val;
}
int find_Kth(Node *x, int K)
{
	int tr = x->lch->size + 1;
	if (tr == K)
	{
		return x->val;
	}
	else if (tr < K)
	{
		return find_Kth(x->rch, K - tr);
	}
	else
	{
		return find_Kth(x->lch, K);
	}
}
int ranking(Node *x, int keys)
{
	if (keys < x->val)
	{
		return ranking(x->lch, keys);
	}
	else if (keys > x->val)
	{
		return ranking(x->rch, keys);
	}
	return x->lch->size + 1;
}

int Upper(int w)
{
	Node *now = rot;
	int result = 2147483600;
	while (now)
	{
		if (now->val > w && now->val < result)
		{
			result = now->val;
		}
		if (w < now->val)
		{
			now = now->lch;
		}
		else
		{
			now = now->rch;
		}
	}
	return result;
}
int Lowwer(int w)
{
	Node *now = rot;
	int result = -2147483600;
	while (now)
	{
		if (now->val < w && now->val > result)
		{
			result = now->val;
		}
		if (w > now->val)
		{
			now = now->rch;
		}
		else
		{
			now = now->lch;
		}
	}
	return result;
}

long long totN;

int main()
{
	totN = read();
	register char opt;
	register long long K;
	for (register long long i = 1; i <= totN; ++i)
	{
		opt = getchar();
		K = read();
		switch (opt)
		{
		case '1':
			insert(rot, K);
			break;
		case '2':
			remove(rot, K);
			break;
		case '3':
			write(ranking(rot, K));
		case '4':
			write(find_Kth(rot, K));
		case '5':
			write(Lowwer(K));
		case '6':
			write(Upper(K));
		default:
			break;
		}
	}
	return 0;
} // LikiBlaze Code
```
