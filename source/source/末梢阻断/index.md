---
title: ' 末梢阻断 '
date: 2020-07-23 19:54:35
description: 一道非常非常水的图论题。
keywords: 我出的题
cover: https://api.r10086.com/%E9%A3%8E%E6%99%AF%E7%B3%BB%E5%88%9716.php
top_img: http://ww1.sinaimg.cn/large/0075LAc9ly1gh13309c5ij30hw05rglx.jpg
---

<div class='tip warning'><p>此题目考试前禁止外泄！！！</p></div>
<div class='tip'><p>本题题目提供者：WYH</p></div>

## 题目背景

<p class='div-border green'>Rhodes的Dr正带领迷迭香和其他几名干员赶往市中心广场。这个城市的地图是一棵树，而市中心广场就在这棵树的根节点。unions有着特殊的能力，他可以制造天灾，使路变得更加难走，来阻挠Dr。而我们Rhodes的后勤队伍会时不时地清理道路，使路更加畅通。迷迭香有着穿越时空的能力，所以她会找到一个好的时机再前往中心广场。</p>

## 题目描述

<p class='div-border red'>Dr现在正在一个叶子节点上。迷迭香的能力可以使我们瞬移到一个节点。但是这种能力并不稳定，瞬移到哪里是随机的。前方会不断传来情报，表示某两个节点之间的路好走了多少，或是难走了多少。迷迭香会问你，从当前所在的节点走到另一个节点要花多少时间。而且，因为她可以穿越时间，到另一个时间的这里再走，所以它还可能问你，在之前的某个时间点上，从一个节点到另一个节点要花多久。</p>

## 输入格式

**操作格式**

```cpp
broke x y w    //代表从x到y节点的路线上所有的边需要的时间增加了w%。
cleaned x y w    //代表从x到y节点的路线上所有的边需要的时间减少了w%。
ask_now x y    //代表询问从目前节点到x节点要多久。
ask_before x y w   //代表询问在时间点w时，从x走到y要多久。
```

<p class='div-border yellow'>

第1行，3个数，<code>M</code><code>N</code><code>P</code><code>S</code>代表有<code>M</code>个点,<code>N</code>条边，初始位置在<code>P</code>，操作数为<code>S</code>。

<br />第<code>2</code>~<code>n+1</code>行，每行一组<code>X</code><code>Y</code><code>W</code>，代表从<code>X</code>节点到<code>Y</code>节点有一条花费为<code>W</code>的边。

<br />第<code>n+2</code>~<code>n+s+1</code>行，每行一个操作。

</p>

## 说明/提示

<p class='div-border blue'>

所有边花费的时间都大于<code>0</code>。

<br />每次操作经历一个时间。

<br />第<code>1</code>次操作时，在时间<code>1</code> 。

<br />数据保证每次的运算结果为整数。

</p>



![迷迭香](https://p.pstatp.com/origin/13786000163ed5593647b)