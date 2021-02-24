---
title: 关于class
date: 2020-07-29 22:39:22
top_img: https://api.r10086.com/%E9%A3%8E%E6%99%AF%E7%B3%BB%E5%88%9710.php
cover: https://tvax3.sinaimg.cn/large/0072Vf1pgy1fodqpglwr2j312w0rnx6p.jpg
tags: 
    - C++基础
description: 关于class的一些基础介绍。
---
# 首先
关于`class`，许多`OIer`便会认为这是一种非常高深的语法，我们无论是现在还是以后的`OI`学习中都用不到。

确实，`class`在`OI`比赛中可以永远不用。

可是，如果这么说的话，除了递归之外，其他函数也可以在`OI`比赛中永远不使用。可是，那我们使用它的原因是什么呢？

对，是因为它可以使程序编写更加容易。

众所周知，`C++`相较于`C`，主要的区别就是在于`C++`增加了关于面向对象的支持，和`STL`。由于我们一般并不去使用面向对象，所以我们学习的语言被戏称为`"C with STL"`。

但是，学习面向对象的基本思想，和有关面向对象的基本语法，真的可以让我们对`C++`语言的认识提升一个档次。

# class是什么？
其实，我们在使用`struct`的时候，就已经对面向对象有了初步的认识。

`struct`其实就相当于是这样：

```cpp
struct Node
{
    int x;
    int y;
    int value;
}
```
相当于：
```cpp
class Node
{
    public:
    int x;
    int y;
    int value;
}
```
没错，也就是说，`struct`就相当于是一个全部都是`public`的`class`。

说到这里，读者对`class`的望而生畏的感觉应当减少了一些。

但是很显然，`class`远远不只有这一点点用处。

# class
`class`的基本用法：
```cpp
class a_class
{
    private:

    public:

}
```
可以看出，这个`class`比刚才的那一个多出了一个`private:`。

那这个`private`有什么用处呢？

在`private`内部的函数，只能在类的内部访问，在外部无论通过什么方法也是无法直接访问的。

在调用类的时候，只能使用`public`内部的函数。

这样就有了一个好处：

我们在使用一个类的时候，不需要管它内部是怎样实现的，只需要调用在`public`中的函数接口即可。

这样，虽然我们再写类的时候要仔细思考接口怎样编写比较方便，但是大大减少了我们在写类外部代码时的思维量。

而毕竟写类只需要写一次，调用可能需奥很多很多次，所以我们总体的思维量还是会有所下降。

# 范例
（下面是我自己在考试的时候写的矩阵快速幂代码：~~其实本来只是想秀给CYC看的~~）
```cpp
#include<bits/stdc++.h>

using namespace std;

class martix
{
private:
	long long nums[6][6];
	clear()
	{
		memset(nums,0,sizeof(nums));
	}
	make_standard()
	{
		memset(nums,0,sizeof(nums));
		for(int i=1;i<=5;i++)
		{
			nums[i][i]=1;
		}
	}
public:
	martix()
	{
		clear();
	}
	martix operator*(martix b)
	{
		martix result;
		for(int i=1;i<=3;i++)
		{
			for(int j=1;j<=3;j++)
			{
				for(int k=1;k<=3;k++)
				{
					result.nums[i][j]+=nums[i][k]*b.nums[k][j]%1000000007;
				}
			}
		}
		return result;
	}
	martix quick_pow(long long k)
	{
		martix result;
		result.make_standard();
		martix base;
		for(register int i=1;i<=5;i++)
		{
			for(register int j=1;j<=5;j++)
			{
				base.nums[i][j]=nums[i][j];
			}
		}
		while(k)
		{
			if(k&1)
			{
				result=result*base;
			}
			k>>=1;
			base=base*base;
		}
		return result;
	}
	void make_fib()
	{
		nums[1][1]=1;
		nums[1][2]=1;
		nums[1][3]=1;
	}
	void make_base()
	{
		nums[1][1]=1;
		nums[1][2]=1;
		nums[2][1]=0;
		nums[2][2]=0;
		nums[3][1]=1;
		nums[3][2]=0;
		nums[2][3]=1;
		nums[1][3]=0;
	}
	void print()
	{
		printf("%I64d\n",nums[1][3]%1000000007);
	}
};

const int maxn=2e7+90;
martix fib;
martix base;
int T;

int main()
{
	freopen("seq.in","r",stdin);
	freopen("seq.out","w",stdout);
	cin>>T;
	int temp;
	for(int i=1;i<=T;i++)
	{
		cin>>temp;
		fib.make_fib();
		base.make_base();
		fib=fib*base.quick_pow(temp-1);
		fib.print();
	}
	return 0;
}

```
本文目前只是写了一丁点皮毛。以后应该会持续更新。