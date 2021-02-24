---
title: STL总结
date: 2021-01-01 19:10:04
tags:
	- C++基础
categories: C++基础
description: 关于STL容器的一些总结。
top_img: https://api.r10086.com/%E6%98%8E%E6%97%A5%E6%96%B9%E8%88%9F2.php
cover: https://tvax4.sinaimg.cn/large/0072Vf1pgy1foxk7hw45cj31hc0u0nhq.jpg
---

`2021`年啦！元旦快乐！

前几天扶苏让我们学STL，然后我就感觉STL是属于语法方面，就去<a herf="https://zh.cppreference.com">cppreference</a>上面看。结果发现<a herf="https://zh.cppreference.com">cppreference</a>上面的STL不仅分散，而且有好多东西都是我们用不到的。所以我就想写个博客整理一下`STL`方面的东西。

`STL`这东西有好多实现比较先进，随着人类的进化`STL`中的好多东西会被实现的越来越好，使用方法有时也会随之变化。所以这篇博客应该会随着`C++`的更新而更新。

<div class='tip' ><p>目前C++版本：C++20。<p></div>

<h2>关于STL容器前缀</h2>
<h3>multi</h3>
代表容器内部的元素可以重复。
<h3>unordered_</h3>
代表此容器基于哈希表，复杂度几乎为常数，但是内存占用较大。
<h3>unordered_multi</h3>
顾名思义，这是上两种特性的结合。

<h2>std::vector</h2>

首先当然是我们最最最基础的`vector`动态数组啦！

`vector`基础函数列表：

| 函数 | 作用 |
| ---- | ---- |
| `push_back` | 从最后加入一个元素 |
| `pop_back` | 弹出最后一个元素 |
| `clear` | 清空 |
| `size` | 总元素个数 |
| `empty` | 返回是否为空 |

由于`vector`可以直接下标访问，大部分函数都用不太到，这里就不再赘述了。

<h2>std::stack</h2>

一个栈。

| 函数 | 作用 |
| ---- | ---- |
| `push` | 从栈顶压入一个元素 |
| `pop` | 弹出栈顶元素 |
| `clear` | 清空 |
| `size` | 总元素个数 |
| `empty` | 返回是否为空 |

<h2>std::queue</h2>

一个队列。

| 函数 | 作用 |
| ---- | ---- |
| `push` | 从队尾加入一个元素 |
| `pop` | 弹出队首元素 |
| `clear` | 清空 |
| `size` | 总元素个数 |
| `empty` | 返回是否为空 |

<h2>std::deque</h2>

一个双端队列。

| 函数 | 作用 |
| ---- | ---- |
| `push_front` | 从队首加入一个元素 |
| `push_back` | 从队尾加入一个元素 |
| `pop_front` | 弹出队首元素 |
| `pop_back` | 弹出队尾元素 |
| `clear` | 清空 |
| `size` | 总元素个数 |
| `empty` | 返回是否为空 |

<h2>std::priority_queue</h2>
优先队列，也就是堆。是一个大根堆。

如果想使用小根堆可以这样：
```cpp
std::priority_queue<int,vector<int>,greater<int> > Heap;
```

| 函数 | 作用 |
| ---- | ---- |
| `push(value)` | 加入一个元素 |
| `pop` | 弹顶 |
| `top` | 返回堆顶值 |
| `size` | 总元素个数 |
| `empty` | 返回是否为空 |

<h2>std::map</h2>

`map`家族有四个容器：`map`,`unordered_map`,`multimap`,`unordered_multimap`。区别上面有说。

`map`的内部实现是一棵红黑树，主要的功能是实现键值映射。

| 函数 | 作用 |
| ---- | ---- |
| `insert(value)` | 加入一个元素 |
| `clear` | 清空 |
| `size` | 总元素个数 |
| `empty` | 返回是否为空 |

而`map`最最最大的一个优点，就是——它可以直接以像数组一样的方式访问它所对应的元素。这也就是说，你可以使用数字，浮点数，字符串，甚至于结构体来当做`map`的数组下标。

<h2>std::set</h2>

`set`家族也有四个容器：`set`,`unordered_set`,`multiset`,`unordered_multiset`。区别上面有说。

`set`的内部实现是一棵红黑树，主要的功能是实现查重。

| 函数 | 作用 |
| ---- | ---- |
| `insert(value)` | 加入一个元素 |
| `clear` | 清空 |
| `size` | 总元素个数 |
| `empty` | 返回是否为空 |
| `find(value)` | 指向键等于 key 的元素的迭代器。若找不到这种元素，则返回end()迭代器。 |
| `count` | 返回匹配特定键的元素数量 |

<h2>std::bitset</h2>

相当于一个布尔数组，可以使用数组下标访问。但是每一个元素只占用一bit。

| 函数 | 作用 |
| ---- | ---- |
| `set` | 将位置为`true`或者提供的值 |
| `reset` | 将位置为`false` |
| `flip` | 翻转位的值 |
| `to_string` | 返回数据的字符串表示 |
| `to_ullong (C++11)` | 返回数据的`unsigned long long`整数表示 |