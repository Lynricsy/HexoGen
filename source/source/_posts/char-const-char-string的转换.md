---
title: char*,const char*,string的转换
date: 2020-07-17 22:13:27
updated: 
tags: 	
	- C++基础
	- 字符串
categories: C++基础
description: 关于char*,const char*,string之间的转换。
top_img: https://api.r10086.com/%E9%A3%8E%E6%99%AF%E7%B3%BB%E5%88%9710.php
cover: https://tvax1.sinaimg.cn/large/0072Vf1pgy1foxkfz74phj31hc0u0qht.jpg
---

# 思考起因

这其实又是一篇主要记录过程的博客。

这次引起我对这一块内容的思考的是我自己出的一道题。但是因为这道题以后可能要用于考试，所以暂时不公布具体题目。

总之，在做这道题的时候，又用到了我上次说到的运算符重载。

可是，这次要计算的数字极其巨大，很可能会爆`string`。

所以我这次只好使用`char*`来重载运算符。

由于直接使用`char*`运算，在最后不容易把各位合起来（速度较慢），所以我只好选择在合并时，先使用string储存，再把多个string合并到一个`char*`中。

这样，就会涉及到`string`与`char*`之间的转换。

# 过程

我以前从来没有使用过`string`到`char*`的转换，对这一块非常不熟悉。于是便去网上搜。

先是看到了这个：

```cpp
string str;
char* charstr;
charstr=str.c_str();
```

发现并不能解决问题。

IDE给我的提示是不能使用`const char*`赋值给`char*`。

自然想到了可以这样：

```cpp
string str;
char* charstr;
charstr=(char*)str.c_str();
```

这一次虽然IDE并没有说我语法错误，可是实际效果是仍无法赋值。

从网上又发现了这样的一个函数：

```cpp
str.data()
```

这个函数返回的是一个`string`除了末尾的`‘\0’`之外的字符串。

结果使用之后仍然是返回的`const char*`，无法赋值。

后来我又从网上的一篇博客中意识到了`const char*`无法给`char*`赋值的原因：

因为`const char*`和`char*`都是指针，如果赋值的话，`char*`也会指向`const char*`，这样就会造成`const char*` 的值被`char*`改变。

所以，想使用`const char*`给`char*`赋值，其实是不可能的。

但是有一个函数：

```cpp
strcpy()
```

所以我们就可以在定义`char*`的时候先new出来一块新的空间，然后再`strcpy`一下即可。

这样问题就解决了。

（虽然看起来很简单，但是我调试了将近一天的时间）

# 结果

所以我们使用`string`转换为`char*`的时候，只需要先写一个`c_str()`，然后再使用`strcpy()`复制即可。